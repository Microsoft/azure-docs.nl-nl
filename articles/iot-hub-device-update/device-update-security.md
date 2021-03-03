---
title: Beveiliging voor het bijwerken van apparaten voor Azure IoT Hub | Microsoft Docs
description: Meer informatie over het bijwerken van apparaten met updates voor IoT Hub zorgt ervoor dat het apparaat veilig wordt bijgewerkt.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: cf05d5f93180db91658d0e94a23359edd5b0f7ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662663"
---
# <a name="device-update-security-model"></a>Beveiligings model voor het bijwerken van apparaten

Update van het apparaat voor IoT Hub biedt een veilige methode voor het implementeren van updates voor de firmware van het apparaat, installatie kopieën en toepassingen op uw IoT-apparaten. De werk stroom biedt een end-to-end beveiligd kanaal met een volledig model voor het bewaren van een keten dat een apparaat kan gebruiken om te bewijzen dat een update wordt vertrouwd, ongewijzigd en opzettelijk.

Elke stap in de werk stroom voor het bijwerken van apparaten wordt beschermd door diverse beveiligings functies en-processen om ervoor te zorgen dat elke stap in de pijp lijn een beveiligde bevinding naar de volgende heeft. De Update-client van het apparaat identificeert en beheert alle illegitimate-update aanvragen. De client controleert ook elke down load om ervoor te zorgen dat de inhoud wordt vertrouwd, ongewijzigd en opzettelijk is.

## <a name="for-solution-operators"></a>Voor oplossings operators

Als oplossings operators updates importeren in hun update-exemplaar van het apparaat, uploadt en controleert de service de binaire update bestanden om ervoor te zorgen dat ze niet zijn gewijzigd of gewisseld door een kwaadwillende gebruiker. Nadat het apparaat is geverifieerd, genereert de update service een intern [Update manifest](./update-manifest.md) met bestands-hashes uit het import manifest en andere meta gegevens. Dit update manifest wordt vervolgens ondertekend door de service voor het bijwerken van het apparaat.

Wanneer de oplossings operator vraagt om een apparaat bij te werken, wordt een ondertekend bericht via het beveiligde IoT Hub kanaal naar het apparaat verzonden. De hand tekening van de aanvraag wordt gevalideerd door de apparaat-Update Agent van het apparaat als authentiek. 

Elke resulterende binaire down load wordt beveiligd door validatie van de hand tekening van de update manifest. Het update manifest bevat de hashes van het binaire bestand, dus wanneer het manifest vertrouwd is, vertrouwt de Update Agent van het apparaat de hashes en komt deze overeen met de binaire bestanden. Zodra de binaire update is gedownload en geverifieerd, wordt deze vervolgens aan het installatie programma op het apparaat door gegeven.

## <a name="for-device-builders"></a>Voor apparaten bouwen

Om ervoor te zorgen dat de service voor het bijwerken van apparaten wordt geschaald naar eenvoudige apparaten met lage prestaties, gebruikt het beveiligings model onbewerkte asymmetrische sleutels en onbewerkte hand tekeningen. Ze gebruiken JSON-gebaseerde indelingen, zoals JSON-webtokens & JSON-websleutels.

### <a name="securing-update-content-via-the-update-manifest"></a>Update-inhoud beveiligen via het update manifest

Het update manifest wordt gevalideerd door twee hand tekeningen te gebruiken. De hand tekeningen worden gemaakt met behulp van een structuur die bestaat uit *handtekening* sleutels en *hoofd* sleutels.

De Update Agent voor apparaten bevat Inge sloten open bare sleutels die worden gebruikt voor alle apparaten die compatibel zijn met apparaat-updates. Dit zijn de *hoofd* sleutels. De bijbehorende persoonlijke sleutels worden beheerd door micro soft.

Micro soft genereert ook een openbaar/persoonlijk sleutel paar dat niet is opgenomen in de Update Agent van het apparaat of op het apparaat is opgeslagen. Dit is de *handtekening* sleutel.

Wanneer een update wordt geïmporteerd in de update van het apparaat voor IoT Hub en het update manifest wordt gegenereerd door de service, ondertekent de service het manifest met de handtekening sleutel en bevat de handtekening sleutel zelf, die is ondertekend met een basis sleutel. Wanneer het update-manifest naar het apparaat wordt verzonden, ontvangt de Update Agent van het apparaat de volgende handtekening gegevens:

1. De handtekening waarde zelf.
2. De algoritme die wordt gebruikt voor het genereren van #1.
3. De informatie over de open bare sleutel van de handtekening sleutel die wordt gebruikt voor het genereren van #1.
4. De hand tekening van de open bare handtekening sleutel in #3.
5. De ID van de open bare sleutel van de basis sleutel die wordt gebruikt voor het genereren van #3.
6. De algoritme die wordt gebruikt voor het genereren van #4.

De Update Agent van het apparaat gebruikt de hierboven gedefinieerde informatie om te controleren of de hand tekening van de open bare ondertekeningssleutel is ondertekend door de hoofd sleutel. De Update Agent van het apparaat valideert vervolgens of de hand tekening van het update manifest is ondertekend door de handtekening sleutel. Als alle hand tekeningen juist zijn, wordt het update-manifest vertrouwd door de apparaat Update Agent. Omdat het update manifest de bestands-hashes bevat die overeenkomen met de update bestanden zelf, kunnen de update bestanden ook worden vertrouwd als de hashes overeenkomen.

Met de hoofd-en ondertekeningssleutel kunnen micro soft regel matig de handtekening sleutel, een beveiligings best practice, samen vouwen.

### <a name="json-web-signature-jws"></a>JSON Web Signature (JWS)

De `updateManifestSignature` wordt gebruikt om ervoor te zorgen dat er niet is `updateManifest` geknoeid met de informatie in de. De `updateManifestSignature` wordt gemaakt met behulp van een JSON-webhandtekening met JSON-Websleutels, waardoor bron verificatie mogelijk is. De hand tekening is een gecodeerde Base64Url-teken reeks met drie secties die worden afgebakend door '. '.  Raadpleeg de Help-methoden van jws_util. h voor het parseren en controleren van JSON-sleutels en-tokens.

JSON Web Signature is een veelgebruikte [voorgestelde IETF-standaard](https://tools.ietf.org/html/rfc7515) voor het ondertekenen van inhoud met behulp van JSON-gebaseerde gegevens structuren. Het is een manier om de integriteit van gegevens te garanderen door de hand tekening van de gegevens te controleren. Meer informatie vindt u in de JWS- [RFC 7515](https://www.rfc-editor.org/info/rfc7515)(JSON Web Signature).

### <a name="json-web-token"></a>JSON Web Token

JSON-webtokens zijn een open, industrie [standaard](https://tools.ietf.org/html/rfc7519) methode voor het veilig vertegenwoordigen van claims tussen twee partijen.

### <a name="root-keys"></a>Hoofd sleutels

Elk apparaat voor het bijwerken van apparaten bevat een aantal hoofd sleutels. Deze sleutels zijn de basis van de vertrouwens relatie voor alle hand tekeningen van de apparaat-update. Elke hand tekening moet worden gekoppeld aan een van deze basis sleutels om als legitiem te worden beschouwd.

De set met hoofd sleutels wordt na verloop van tijd gewijzigd, omdat het goed is om regel matig aanmeldings sleutels voor beveiligings doeleinden te draaien. Als gevolg hiervan moet de Update Agent-software van het apparaat zichzelf bijwerken met de meest recente set basis sleutels. 

### <a name="signatures"></a>Handtekening

Alle hand tekeningen worden toegevoegd aan een ondertekende (open bare) sleutel die is ondertekend door een van de hoofd sleutels. In de hand tekening wordt aangegeven welke basis sleutel is gebruikt voor het ondertekenen van de handtekening sleutel. 

Een update-agent van het apparaat moet hand tekeningen valideren door eerst te valideren dat de hand tekening van de ondertekende (open bare) sleutel geldig is, geldige en ondertekend door een van de goedgekeurde basis sleutels. Zodra de handtekening sleutel is gevalideerd, kan de hand tekening zelf worden gevalideerd met de open bare sleutel voor vertrouwd ondertekenen.

Ondertekeningssleutels worden op een veel snellere uitgebracht gedraaid dan hoofd sleutels, waardoor berichten die zijn ondertekend door verschillende verschillende ondertekeningssleutels, worden verwacht. 

Het intrekken van een ondertekeningssleutel wordt beheerd door de service voor het bijwerken van apparaten, zodat gebruikers zich niet hoeven op te slaan voor het ondertekenen van sleutels. Gebruik altijd de handtekening sleutel bij een hand tekening.

### <a name="receiving-updates"></a>Updates ontvangen

Update aanvragen die worden ontvangen door een apparaat Update Agent bevatten een ondertekend UM-document (update manifest). De agent moet valideren dat de hand tekening van de UM juist is en intact is. Dit wordt gedaan door te controleren of de handtekening sleutel van de UM-hand tekening is ondertekend door een geldige hoofd sleutel. Als u klaar bent, valideert de agent de UM-hand tekening op basis van de handtekening sleutel.

Zodra de UM-hand tekening is gevalideerd, kan de apparaat-Update agent deze als een ' bron van waarheid ' vertrouwen. Alle verdere beveiligings vertrouwensrelaties van deze bron. 

De UM bevat Url's en bestands-hashes van inhoud die u kunt downloaden en installeren. Zodra de agent een update-binair heeft gedownload, moet deze de update valideren op basis van de bestands-hash die in de UM is gevonden. Dit biedt een transitief vertrouwens model voor Download validatie. Het garandeert niet alleen dat de inhoud intact (niet gewijzigd) is, maar ook bevestigt dat wat er is gedownload inderdaad zou zijn bedoeld om te worden gedownload. 

### <a name="securing-the-device"></a>Het apparaat beveiligen

Het is belang rijk om ervoor te zorgen dat bijwerk-gerelateerde beveiligings assets op het apparaat goed zijn beveiligd en beveiligd. Activa zoals hoofd sleutels moeten worden beschermd tegen wijzigingen. Er zijn verschillende manieren om dit te doen, zoals het gebruik van beveiligings apparaten (TPM, SGX, HSM, andere beveiligings apparaten) of zelfs het afwijzen van de code in de Update-Agent van het apparaat. Hiervoor moet de code van de Update Agent van het apparaat digitaal zijn ondertekend en de ondersteuning voor code-integriteit van het systeem is ingeschakeld ter bescherming tegen schadelijke wijzigingen van de agent code.

Aanvullende beveiligings maatregelen kunnen worden gerechtvaardigd, zoals het garanderen dat ze van component tot onderdeel op een veilige manier worden uitgevoerd. U kunt bijvoorbeeld een specifiek geïsoleerd account registreren om de verschillende onderdelen uit te voeren. En het beperken van netwerk communicatie (bijvoorbeeld REST API-aanroepen) naar localhost.

**[Volgende stap: meer informatie over hoe het bijwerken van apparaten gebruikmaakt van Azure RBAC](.\device-update-control-access.md)**