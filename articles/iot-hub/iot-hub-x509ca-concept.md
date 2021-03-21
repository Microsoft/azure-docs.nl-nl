---
title: Concepten van Azure IoT Hub X. 509-beveiliging | Microsoft Docs
description: 'Concept: informatie over de waarde X. 509 certificaten certificerings instanties in IoT-apparaat-productie en verificatie.'
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 877200cbafbe68fa6161025572abfddad651e172
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490717"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Conceptuele uitleg van X. 509 CA-certificaten in de IoT-industrie

In dit artikel wordt de waarde voor het gebruik van X. 509 Certificate Authority (CA)-certificaten in IoT-apparaten productie en-verificatie beschreven om IoT Hub. Het bevat informatie over het instellen van de toeleverings keten en het markeren van voor delen.

In dit artikel wordt het volgende beschreven:

* Wat X. 509 CA-certificaten zijn en hoe u deze kunt ophalen

* Uw X. 509 CA-certificaat registreren bij IoT Hub

* Een productie leverings keten instellen voor X. 509 authenticatie op basis van een certificerings instantie

* Hoe apparaten die zijn ondertekend met X. 509-CA verbinding maken met IoT Hub

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Overzicht

X. 509 certificerings instantie (CA)-verificatie is een benadering voor het verifiëren van apparaten om IoT Hub te gebruiken met behulp van een methode waarmee het maken van de apparaat-id en het levenscyclus beheer in de toeleverings keten aanzienlijk wordt vereenvoudigd.

Een onderscheidings kenmerk van de X. 509 CA-verificatie is een een-op-veel-relatie met de downstream-apparaten van een CA-certificaat. Met deze relatie wordt de registratie van een wille keurig aantal apparaten in IoT Hub mogelijk gemaakt door een X. 509 CA-certificaat te registreren, anders moeten unieke certificaten vooraf voor elk apparaat worden geregistreerd voordat een apparaat verbinding kan maken. Deze een-op-veel-relatie vereenvoudigt ook de beheer bewerkingen voor de levens cyclus van apparaten.

Een ander belang rijk kenmerk van de X. 509 CA-verificatie is een vereenvoudiging van de logistiek van toeleverings ketens. Voor de beveiliging van apparaten is het vereist dat elk apparaat een uniek geheim als basis voor vertrouwens relatie bevat. In verificatie op basis van certificaten is dit geheim een persoonlijke sleutel. Een typische stroom voor de fabricage van apparaten omvat meerdere stappen en bewaarders. Het veilig beheren van persoonlijke sleutels van apparaten in meerdere beheerders en het onderhouden van vertrouwen is moeilijk en kostbaar. Het gebruik van certificerings instanties lost dit probleem op door elke beheerder te ondertekenen in een cryptografische vertrouwens keten in plaats van deze te belasten met persoonlijke sleutels van apparaten. Elke beheerder op zijn beurt apparaten ondertekent aan de respectieve proces stap van de productie stroom. Het totale resultaat is een optimale toeleverings keten met ingebouwde verantwoordelijkheid voor het gebruik van de cryptografische vertrouwens keten. Het is een goed idee dat dit proces de meest beveiliging oplevert wanneer apparaten hun unieke persoonlijke sleutels beveiligen. Daarom is het raadzaam om het gebruik van HSM (hardware Secure modules) te gebruiken voor het intern genereren van persoonlijke sleutels die het licht van de dag nooit zullen zien.

Dit artikel biedt een end-to-end-weer gave van het gebruik van de X. 509 CA-verificatie, van de instelling van de toeleverings keten tot de verbinding met het apparaat en het gebruik van een echt wereld voorbeeld om inzicht te krijgen in versterken.

U kunt ook inschrijvings groepen gebruiken met Azure IoT Hub Device Provisioning Service (DPS) voor het afhandelen van het inrichten van apparaten naar hubs. Zie [zelf studie: meerdere X. 509-apparaten inrichten met registratie groepen](../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md)voor meer informatie over het gebruik van DPS om X. 509-certificaat apparaten in te richten.

## <a name="introduction"></a>Inleiding

Het X. 509 CA-certificaat is een digitaal certificaat waarvan de houder andere certificaten mag ondertekenen. Dit digitale certificaat is X. 509 omdat het voldoet aan een standaard voor de certificaat opmaak die is voorgeschreven door de RFC 5280-standaard van de IETF en een certificerings instantie (CA) is, omdat de houder andere certificaten kan ondertekenen.

Het gebruik van X. 509 CA is het meest begrijpelijk voor een concreet voor beeld. Overweeg bedrijf-X, een maker van Smart-X-widgets, ontworpen voor professionele installatie. Bedrijf-X verstuurt zowel productie als installatie. De IT-mede werker van de fabrikant-Y voor het fabriceren van de Smart-X-widgets en de technicus van de service provider-Z om te installeren. Bedrijf-X wil dat Smart-X-widget rechtstreeks van Factory-Y naar Technicus-Z wordt geleverd voor installatie en dat deze rechtstreeks verbinding maakt met het exemplaar van IoT Hub van de bedrijf-x na installatie zonder verdere tussen komst van bedrijf-X. Om dit te doen, moet bedrijf-X enkele eenmalige installatie bewerkingen uitvoeren op een prime Smart-X-widget voor automatische verbinding. Met het end-to-end-scenario is de rest van dit artikel als volgt gestructureerd:

* Het X. 509-CA-certificaat verkrijgen

* X. 509 CA-certificaat registreren bij IoT Hub

* Apparaten ondertekenen in een certificaat vertrouwens keten

* Apparaatverbinding

## <a name="acquire-the-x509-ca-certificate"></a>Het X. 509-CA-certificaat verkrijgen

Bedrijf-X heeft de mogelijkheid om een X. 509 CA-certificaat te kopen bij een open bare basis certificerings instantie of door een te maken via een zelfondertekend proces. De ene optie is optimaal, afhankelijk van het toepassings scenario. Ongeacht de optie omvat het proces twee fundamentele stappen, het genereren van een openbaar/persoonlijk sleutel paar en het ondertekenen van de open bare sleutel in een certificaat.

![Stroom voor het genereren van een X509CA-certificaat](./media/iot-hub-x509ca-concept/csr-flow.png)

Meer informatie over hoe u deze stappen kunt uitvoeren, is afhankelijk van verschillende service providers.

### <a name="purchasing-an-x509-ca-certificate"></a>Een X. 509-CA-certificaat kopen

Het aanschaffen van een CA-certificaat heeft als voor deel dat een bekende basis certificerings instantie een vertrouwde derde partij kan hebben om te kunnen betalen voor de rechtmatigheid van IoT-apparaten wanneer de apparaten verbinding maken. Bedrijf-X zou deze optie kiezen als ze een slimme X-widget hebben om te communiceren met producten of services van derden na de eerste verbinding met IoT Hub.

Als u een X. 509 CA-certificaat wilt kopen, kiest bedrijf-X een service provider basis certificerings instanties. Een zoek opdracht op internet naar de zin ' basis-CA ' levert goede leads. De basis-CA leidt bedrijf-X over het maken van het open bare/persoonlijke sleutel paar en het genereren van een aanvraag voor certificaat ondertekening (CSR) voor hun services. Een CSR is het formele proces voor het Toep assen van een certificaat van een certificerings instantie. Het resultaat van deze aankoop is een certificaat voor gebruik als een CA-certificaat. Gezien de alomtegenwoordigheid van X. 509-certificaten is het certificaat waarschijnlijk correct ingedeeld in de IETF RFC 5280-standaard.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Een Self-Signed X. 509-CA-certificaat maken

Het proces voor het maken van een Self-Signed X. 509 CA-certificaat is vergelijkbaar met het aanschaffen van een ondertekenaar van derden, zoals de basis certificerings instantie. In ons voor beeld wordt het CA-certificaat door bedrijf-X ondertekend in plaats van een basis certificerings instantie. Bedrijf-X kan deze optie kiezen om te testen totdat ze klaar zijn om een CA-certificaat aan te schaffen. Bedrijf-X kan ook gebruikmaken van een zelfondertekend X. 509 CA-certificaat in productie als Smart-X-widget niet is bedoeld om verbinding te maken met services van derden buiten de IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Het X. 509-certificaat registreren bij IoT Hub

Bedrijf-X moet de certificerings instantie X. 509 registreren bij IoT Hub, waar deze moet worden gebruikt voor het verifiëren van Smart X-widgets wanneer ze verbinding maken. Dit is een eenmalig proces waarmee u een wille keurig aantal Smart-X-widget apparaten kunt verifiëren en beheren. Dit is een eenmalig proces vanwege een een-op-veel-relatie tussen CA-certificaat en apparaat certificaten die zijn ondertekend door het CA-certificaat of een tussenliggend certificaat. Deze relatie vormt een van de belangrijkste voor delen van het gebruik van de X. 509 CA-verificatie methode. Het is ook mogelijk om afzonderlijke certificaat vingerafdrukten te uploaden voor elk apparaat met een slimme X-widget, waardoor de operationele kosten worden toegevoegd.

Het registreren van het X. 509-CA-certificaat is een proces dat uit twee stappen bestaat, het uploaden van het certificaat en het certificaat.

![Een X509CA-certificaat registreren](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X. 509 CA-certificaat uploaden

Het X. 509 CA-certificaat upload proces is alleen dat, upload het CA-certificaat naar IoT Hub. IoT Hub verwacht het certificaat in een bestand. Bedrijf-X uploadt gewoon het certificaat bestand. Het certificaat bestand mag in geen geval een persoonlijke sleutel bevatten. Best practices van standaarden voor open bare-sleutel infrastructuur (PKI)-mandaten die de kennis van bedrijfs-en particuliere zaken in dit geval uitsluitend bevinden in het bedrijf-X.

### <a name="proof-of-possession-of-the-certificate"></a>Bewijs van het bezit van het certificaat

Het X. 509 CA-certificaat, net als elk digitaal certificaat, is open bare gegevens die vatbaar zijn voor het afluis teren. Zo kan een Eavesdropper een certificaat onderscheppen en proberen het te uploaden als hun eigen. In ons voor beeld moet IoT Hub ervoor zorgen dat het CA-certificaat bedrijf-X helemaal geen deel uitmaakt van het bedrijf-X. Dit doet u door een uitdagend bedrijf-X, om aan te tonen dat ze in feite het certificaat hebben via een [pop-stroom (bewijs van eigendom)](https://tools.ietf.org/html/rfc5280#section-3.1). De stroom van het bewijs van zijn bezit is IoT Hub het genereren van een wille keurig getal dat door bedrijf-X wordt ondertekend met behulp van de bijbehorende persoonlijke sleutel. Als de aanbevolen procedures van het bedrijf-X gevolgd door de PKI en de persoonlijke sleutel worden beveiligd, zouden alleen ze in de juiste stand kunnen reageren op de uitdaging van het bewijs. IoT Hub gaat om het CA-certificaat van X. 509 te registreren na een geslaagde reactie van de uitdaging van het bewijs.

Een geslaagde reactie op de controle van de bewijs van het bezit van IoT Hub voltooit de X. 509 CA-registratie.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Apparaten ondertekenen in een certificaat vertrouwens keten

IoT vereist dat elk apparaat een unieke identiteit bezit. Deze identiteiten bevinden zich in de vorm van certificaten voor verificatie schema's op basis van certificaten. In ons voor beeld betekent dit dat elke slimme X-widget een uniek apparaat certificaat moet hebben. Hoe worden er in de toeleverings keten voor dit bedrijf-X-installatie?

Een manier om dit te doen, is door vooraf certificaten te genereren voor Smart-X-widgets en de kennis van overeenkomende persoonlijke sleutels van het apparaat te belasten met supply chain-partners. Voor bedrijf-X betekent dit dat de Factory-Y en Technicus-Z worden toevertrouwd. Hoewel dit een geldige methode is, zijn er uitdagingen die moeten worden Weggen omen om de volgende zaken te vertrouwen:

1. Als u persoonlijke sleutels van apparaten wilt delen met supply chain-partners, wordt het bouwen van vertrouwen in de toeleverings keten kostbaar, behalve het negeren van de aanbevolen procedures voor PKI van het nooit delen van persoonlijke sleutels. Dit betekent hoofd systemen als beveiligde ruimten op persoonlijke sleutels van het apparaat, en processen zoals periodieke beveiligings controles moeten worden geïnstalleerd. Voeg kosten toe aan de toeleverings keten.

2. Een veilige accounting voor apparaten in de toeleverings keten en deze later beheren in de implementatie verandert in een een-op-een-taak voor elk sleutel-naar-apparaat-paar vanaf het punt dat door het unieke certificaat (dus met de persoonlijke sleutel) van het apparaat wordt gegenereerd. Dit belet het groeps beheer van apparaten, tenzij het concept van groepen expliciet is ingebouwd in het proces. Het beheer van de levens cyclus van het apparaat en het beheren van apparaten wordt daarom een zware werk belasting. In ons voor beeld draagt bedrijf-X bij aan deze last.

X. 509 CA-certificaat authenticatie biedt elegante oplossingen voor problemen met Afore die worden vermeld door het gebruik van certificaat ketens. Een certificaat keten resulteert van een certificerings instantie die een tussenliggende CERTIFICERINGs instantie ondertekent die op zijn beurt een andere tussenliggende CERTIFICERINGs instantie ondertekent, en gaat dus door totdat een laatste tussenliggende CA een apparaat ondertekent. In ons voor beeld: bedrijf-X tekent Factory-Y, dat op zijn beurt de ondertekent van de technicus-Z die het ' Smart-X-widget ' ondertekent.

![Hiërarchie van certificaat keten](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Boven de trapsgewijze certificaten in de keten wordt de logische hand van de Autoriteit weer gegeven. Veel toeleverings ketens volgen deze logische hand waarbij elke tussenliggende CA zich bij de keten aanmeldt tijdens het ontvangen van alle upstream-CA-certificaten, en de laatste tussenliggende CA uiteindelijk elk apparaat ondertekent en alle CA-certificaten van de keten in het apparaat injecteren. Dit is gebruikelijk wanneer het contract productie bedrijf met een hiërarchie van fabrieken een bepaalde Factory heeft voor het uitvoeren van de productie. Hoewel de hiërarchie meerdere niveaus diep kan zijn (bijvoorbeeld op geografie/product type/productie regel), is alleen de Factory aan het eind te vinden met het apparaat, maar de keten wordt vanaf de bovenkant van de hiërarchie onderhouden.

Alternatieve ketens kunnen verschillende tussenliggende CA-interactie hebben met het apparaat, in welk geval de certificerings instantie met het apparaat de certificaat keten inhoud op dat punt injecteert. Hybride modellen zijn ook mogelijk waarbij slechts een deel van de CA fysieke interactie met het apparaat heeft.

In ons voor beeld communiceren zowel factory-Y als Technicus-Z met de Smart-X-widget. Terwijl het bedrijf-X eigenaar is van Smart-X-widget, communiceert het eigenlijk niet in de hele toeleverings keten. De certificaat keten van vertrouwen voor Smart-X-widget vormt daarom het bedrijf-X-ondertekening fabriek-Y, dat op zijn beurt Technicus-Z aantekent en vervolgens de laatste hand tekening voor Smart-X-widget levert. De fabricage en installatie van Smart-X-widget bestaan uit de fabriek-Y en Technicus-Z met behulp van hun respectieve tussenliggende CA-certificaten om elke en alle slimme X-widgets te ondertekenen. Het eind resultaat van dit hele proces is slimme X-widgets met unieke certificaat certificaten en vertrouwens ketens van certificaten tot het bedrijf-X CA-certificaat.

![Vertrouwens relatie tussen de certificaten van een bedrijf en de certificaten van een ander bedrijf](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Dit is een goed punt om de waarde van de methode X. 509 CA te controleren. In plaats van certificaten vooraf te genereren en af te leveren voor elke slimme X-widget in de toeleverings keten, moest het bedrijf-X slechts één keer de fabrieks-Y ondertekenen. In plaats van dat alle apparaten in de levens cyclus van het apparaat moeten worden gevolgd, kan bedrijfs-X nu apparaten traceren en beheren via groepen die natuurlijk afkomstig zijn van het toeleverings keten proces, bijvoorbeeld apparaten die zijn geïnstalleerd door Technicus-Z na juli van een jaar.

Ten minste de verificatie methode van de certificerings instantie in de CA-behandelings keten van het apparaat is niet zo klein. Vanwege het proces van de certificaat keten worden de acties van elk lid in de keten cryptografisch vastgelegd en geverifieerd.

Dit proces is afhankelijk van bepaalde hypo Thesen die moeten worden afgesteld voor volledigheid. Hiervoor is een onafhankelijke combi natie van een uniek openbaar/persoonlijk sleutel paar nodig en de persoonlijke sleutel wordt beveiligd op het apparaat. Gelukkig zijn beveiligde Silicon chips in de vorm van HSM (hardware Secure modules) die kunnen worden gebruikt voor het intern genereren van sleutels en het beveiligen van persoonlijke sleutels. Bedrijf-X hoeft slechts een van deze chips toe te voegen aan de onderdelen stuk lijst van een slimme X-widget.

## <a name="device-connection"></a>Apparaat-verbinding

Voor gaande secties is er verbinding gemaakt met het apparaat. Door eenvoudigweg een X. 509 CA-certificaat te registreren om één keer te IoT Hub, hoe kunnen miljoenen apparaten van de eerste keer worden verbonden en geauthenticeerd?  Simple via dezelfde stroom voor het uploaden van certificaten en het bewijs van het bezit zijn van het certificaat dat we eerder hebben ondervonden met het registreren van het X. 509 CA-certificaat.

Apparaten die zijn vervaardigd voor X. 509 CA-verificatie zijn uitgerust met unieke apparaten en een certificaat keten van de respectieve productie toeleverings keten. De verbinding van het apparaat, zelfs voor de eerste keer, gebeurt in een proces dat uit twee stappen bestaan: certificaat keten uploaden en bewijs van eigendom.

Tijdens het uploaden van de certificaat keten uploadt het apparaat het unieke certificaat van het apparaat samen met de certificaat keten die erin is geïnstalleerd om te IoT Hub. Met behulp van het vooraf geregistreerde X. 509 CA-certificaat kan IoT Hub cryptografisch een aantal dingen valideren, of de geüploade certificaat keten intern consistent is en of de keten afkomstig is van de geldige eigenaar van het X. 509 CA-certificaat. Net als bij de X. 509 CA-registratie proces zou IoT Hub een bewijs van een onderzoek naar de vraag naar een druk proces initiëren om te controleren of de keten en het certificaat van het apparaat daad werkelijk deel uitmaakt van het apparaat. Dit doet u door een wille keurige uitdaging te genereren die door het apparaat wordt ondertekend met behulp van de persoonlijke sleutel voor validatie door IoT Hub. Een geslaagde reactie wordt IoT Hub om het apparaat als authentiek te accepteren en verbinding te verlenen.

In ons voor beeld zou elk Slim X-widget het unieke certificaat van het apparaat uploaden, samen met de fabrieks-Y en Technicus-Z X. 509 CA-certificaten en vervolgens reageren op de bewijs van zijn of haar eigen uitdaging van IoT Hub.

![Stroom van het ene naar het andere certificaat, pop-Challenge van hub](./media/iot-hub-x509ca-concept/device-pop-flow.png)

U ziet dat de basis van vertrouwens relatie wordt aangehouden bij het beveiligen van persoonlijke sleutels, inclusief persoonlijke sleutels van apparaten. We kunnen daarom het belang van beveiligde Silicon chips in de vorm van hardwarematige beveiligings modules (HSM) voor het beschermen van persoonlijke sleutels van apparaten niet zwaar belasten, en de totale best practice van het nooit delen van persoonlijke sleutels, zoals een fabriek die een andere betrouwt met zijn persoonlijke sleutel.
