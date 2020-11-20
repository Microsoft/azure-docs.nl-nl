---
title: Roll X. 509-certificaten in azure IoT Hub Device Provisioning Service
description: Hoe kan ik X. 509-certificaten met het DPS-exemplaar (Device Provisioning Service) implementeren
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: bf8b1e04e11dee4e636826430838a467fe034e3f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951125"
---
# <a name="how-to-roll-x509-device-certificates"></a>Hoe kan ik X. 509-apparaat certificaten

Tijdens de levens cyclus van uw IoT-oplossing moet u certificaten totaliseren. Twee van de belangrijkste redenen voor Rolling certificaten zijn een schending van de beveiliging en het verlopen van certificaten. 

Rolling certificaten is een beveiligings best practice om uw systeem te helpen beveiligen in geval van een schending. Als onderdeel van een [overtredings methodologie](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)heeft micro soft de nood zaak voor het opnieuw activeren van beveiligings processen samen met preventieve maat regelen te nemen. Als onderdeel van deze beveiligings processen moet u de certificaten van het apparaat meenemen. De frequentie waarmee u uw certificaten inrollen, is afhankelijk van de beveiligings behoeften van uw oplossing. Klanten met oplossingen waarbij zeer gevoelige gegevens worden betrokken, kunnen dagelijks certificaten samen vouwen, terwijl anderen hun certificaten elk paar jaar draaien.

Certificaten van het Rolling apparaat moeten het certificaat bijwerken dat is opgeslagen op het apparaat en de IoT-hub. Daarna kan het apparaat opnieuw worden ingericht met de IoT-hub met behulp van de normale [inrichting](about-iot-dps.md#provisioning-process) van de Device Provisioning Service (DPS).


## <a name="obtain-new-certificates"></a>Nieuwe certificaten verkrijgen

Er zijn veel manieren om nieuwe certificaten te verkrijgen voor uw IoT-apparaten. Dit zijn onder andere het verkrijgen van certificaten van de fabriek, het genereren van uw eigen certificaten en het maken van een certificaat door derden te beheren. 

Certificaten zijn door elkaar ondertekend om een vertrouwens keten te vormen van een basis-CA-certificaat naar een [blad certificaat](concepts-x509-attestation.md#end-entity-leaf-certificate). Een handtekening certificaat is het certificaat dat wordt gebruikt om het blad certificaat aan het einde van de vertrouwens keten te ondertekenen. Een handtekening certificaat kan een basis-CA-certificaat of een tussenliggend certificaat in een vertrouwens keten zijn. Zie [X. 509-certificaten](concepts-x509-attestation.md#x509-certificates)voor meer informatie.
 
Er zijn twee verschillende manieren om een handtekening certificaat te verkrijgen. De eerste manier, die wordt aanbevolen voor productie systemen, is een handtekening certificaat aanschaffen bij een basis certificerings instantie (CA). Op deze manier wordt de beveiliging gekoppeld aan een vertrouwde bron. 

De tweede manier is het maken van uw eigen X. 509-certificaten met een hulp programma zoals OpenSSL. Deze aanpak is ideaal voor het testen van X. 509-certificaten, maar biedt een aantal garanties rond de beveiliging. U wordt aangeraden deze methode alleen te gebruiken voor het testen van uw eigen CA-provider.
 

## <a name="roll-the-certificate-on-the-device"></a>Het certificaat op het apparaat samen vouwen

Certificaten op een apparaat moeten altijd worden opgeslagen op een veilige plaats zoals een [Hardware Security module (hsm)](concepts-service.md#hardware-security-module). De manier waarop u apparaat certificaten inrollen, is afhankelijk van hoe ze zijn gemaakt en geïnstalleerd op de apparaten in de eerste plaats. 

Als u uw certificaten van een derde partij hebt ontvangen, moet u kijken hoe ze hun certificaten draaien. Het proces kan worden opgenomen in uw indeling, of het kan een afzonderlijke service zijn die ze bieden. 

Als u uw eigen apparaten wilt beheren, moet u uw eigen pijp lijn bouwen om certificaten bij te werken. Zorg ervoor dat zowel oude als nieuwe blad certificaten dezelfde common name (CN) hebben. Door dezelfde CN te hebben, kan het apparaat zichzelf opnieuw inrichten zonder een duplicaat registratie record te maken. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Het certificaat in de IoT-hub samen vouwen

Het certificaat van het apparaat kan hand matig worden toegevoegd aan een IoT-hub. Het certificaat kan ook worden geautomatiseerd met behulp van een Device Provisioning service-exemplaar. In dit artikel wordt ervan uitgegaan dat er een Device Provisioning service-exemplaar wordt gebruikt ter ondersteuning van automatische inrichting.

Wanneer een apparaat voor het eerst wordt ingericht door automatische inrichting, wordt het opgestart en wordt er contact gemaakt met de inrichtings service. De inrichtings service reageert door een identiteits controle uit te voeren voordat u een apparaat-id in een IoT-hub maakt met behulp van het Leaf-certificaat van het apparaat als de referentie. De inrichtings service vertelt vervolgens het apparaat aan welke IoT-hub het is toegewezen, en het apparaat gebruikt vervolgens het Leaf-certificaat om de IoT-hub te verifiëren en er verbinding mee te maken. 

Zodra een nieuw blad certificaat naar het apparaat is gedistribueerd, kan het geen verbinding meer maken met de IoT-hub, omdat het een nieuw certificaat gebruikt om verbinding te maken. De IoT-hub herkent het apparaat alleen met het oude certificaat. Het resultaat van de verbindings poging van het apparaat is een ' niet-geautoriseerde ' verbindings fout. Om deze fout op te lossen, moet u de inschrijvings vermelding voor het apparaat bijwerken naar account voor het nieuwe blad certificaat van het apparaat. Vervolgens kan de inrichtings service de gegevens van het IoT Hub Device-REGI ster zo nodig bijwerken wanneer het apparaat opnieuw wordt ingericht. 

Een mogelijke uitzonde ring op deze verbindings fout is een scenario waarin u een [registratie groep](concepts-service.md#enrollment-group) voor uw apparaat hebt gemaakt in de inrichtings service. Als u in dit geval de basis-of tussenliggende certificaten in de certificaat keten van het apparaat niet rolt, wordt het apparaat herkend als het nieuwe certificaat deel uitmaakt van de vertrouwens keten die in de registratie groep is gedefinieerd. Als dit scenario zich voordoet als een reactie op een inbreuk op de beveiliging, moet u ten minste de specifieke certificaat certificaten in de groep die worden beschouwd als geschonden, niet toestaan. Zie [specifieke apparaten in een registratie groep weigeren](./how-to-revoke-device-access-portal.md#disallow-specific-devices-in-an-enrollment-group)voor meer informatie.

Het bijwerken van inschrijvings vermeldingen voor gerollte certificaten wordt uitgevoerd op de pagina **inschrijvingen beheren** . Voer de volgende stappen uit om deze pagina te openen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar het IOT hub Device Provisioning service exemplaar met de inschrijvings vermelding voor uw apparaat.

2. Klik op **Inschrijvingen beheren**.

    ![Inschrijvingen beheren](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Hoe u de registratie vermelding bijwerkt, is afhankelijk van het feit of u afzonderlijke inschrijvingen of groeps registraties gebruikt. Ook de aanbevolen procedures variëren, afhankelijk van of u certificaten doorgeeft vanwege een schending van de beveiliging of het verlopen van het certificaat. In de volgende secties wordt beschreven hoe u deze updates verwerkt.


## <a name="individual-enrollments-and-security-breaches"></a>Individuele inschrijvingen en inbreuken op de beveiliging

Als u certificaten doorgeeft als reactie op een schending van de beveiliging, moet u de volgende aanpak gebruiken waarmee het huidige certificaat onmiddellijk wordt verwijderd:

1. Klik op **afzonderlijke inschrijvingen** en klik op de vermelding registratie-id in de lijst. 

2. Klik op de knop **Huidig certificaat verwijderen** en klik vervolgens op het mappictogram om het nieuwe certificaat te selecteren dat u voor de inschrijvings vermelding wilt uploaden. Klik op **Opslaan** wanneer u klaar bent.

    Deze stappen moeten worden uitgevoerd voor het primaire en secundaire certificaat als beide worden aangetast.

    ![Afzonderlijke registraties beheren met een schending van beveiliging](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Zodra het aangetaste certificaat van de inrichtings service is verwijderd, kan het certificaat nog steeds worden gebruikt voor het maken van verbindingen met de IoT-hub zolang er al een apparaatregistratie bestaat. U kunt deze twee manieren verhelpen: 

    De eerste manier is om hand matig naar uw IoT-hub te navigeren en de apparaatregistratie die is gekoppeld aan het aangetaste certificaat, onmiddellijk te verwijderen. Wanneer het apparaat opnieuw wordt ingericht met een bijgewerkt certificaat, wordt er een nieuwe apparaatregistratie gemaakt.     

    ![Apparaatregistratie voor IoT hub verwijderen](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    De tweede manier is de ondersteuning voor opnieuw inrichten te gebruiken om het apparaat opnieuw in te richten op dezelfde IoT-hub. Deze aanpak kan worden gebruikt om het certificaat te vervangen door de apparaatregistratie van de IoT-hub. Zie [apparaten opnieuw inrichten](how-to-reprovision.md)voor meer informatie.

## <a name="individual-enrollments-and-certificate-expiration"></a>Individuele inschrijvingen en verlopen van certificaten

Als u certificaten laat verlopen voor het afhandelen van certificaat verloopt, moet u de configuratie van het secundaire certificaat als volgt gebruiken om de uitval tijd te verminderen voor apparaten die proberen in te richten.

Later wanneer het secundaire certificaat bijna is verlopen en moet worden gedistribueerd, kunt u met de primaire configuratie draaien. Als u de primaire en secundaire certificaten op deze manier draait, vermindert u de downtime voor apparaten die worden ingericht.


1. Klik op **afzonderlijke inschrijvingen** en klik op de vermelding registratie-id in de lijst. 

2. Klik op **secundair certificaat** en klik vervolgens op het mappictogram om het nieuwe certificaat te selecteren dat u voor de inschrijvings vermelding wilt uploaden. Klik op **Opslaan**.

    ![Afzonderlijke inschrijvingen beheren met het secundaire certificaat verloop](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Later wanneer het primaire certificaat is verlopen, keert u terug en verwijdert u dat primaire certificaat door op de knop **Huidig certificaat verwijderen** te klikken.

## <a name="enrollment-groups-and-security-breaches"></a>Inschrijvings groepen en schending van beveiliging

Als u een groeps registratie wilt bijwerken als reactie op een schending van de beveiliging, moet u een van de volgende benaderingen gebruiken waarmee de huidige basis-CA of het tussenliggende certificaat direct wordt verwijderd.

#### <a name="update-compromised-root-ca-certificates"></a>Beschadigde basis-CA-certificaten bijwerken

1. Klik op het tabblad **certificaten** voor uw Device Provisioning service-exemplaar.

2. Klik op het aangetaste certificaat in de lijst en klik vervolgens op de knop **verwijderen** . Bevestig het verwijderen door de naam van het certificaat in te voeren en op **OK** te klikken. Herhaal dit proces voor alle aangetaste certificaten.

    ![Basis-CA-certificaat verwijderen](./media/how-to-roll-certificates/delete-root-cert.png)

3. Volg de stappen die worden beschreven in [geverifieerde CA-certificaten configureren](how-to-verify-certificates.md) om nieuwe basis-CA-certificaten toe te voegen en te verifiëren.

4. Klik op het tabblad **inschrijvingen beheren** voor uw Device Provisioning service-exemplaar en klik op de lijst **registratie groepen** . Klik op de naam van de registratie groep in de lijst.

5. Klik op **CA-certificaat** en selecteer uw nieuwe basis-CA-certificaat. Klik vervolgens op **Opslaan**. 

    ![Het nieuwe basis-CA-certificaat voor een aangetast certificaat selecteren](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Zodra het aangetaste certificaat van de inrichtings service is verwijderd, kan het certificaat nog steeds worden gebruikt voor het maken van verbindingen met de IoT-hub zolang er al apparaatregistratie aanwezig zijn. U kunt deze twee manieren verhelpen: 

    De eerste manier is om hand matig naar uw IoT-hub te navigeren en de apparaatregistratie die is gekoppeld aan het aangetaste certificaat, onmiddellijk te verwijderen. Wanneer uw apparaten vervolgens weer worden ingericht met bijgewerkte certificaten, wordt er voor elk apparaat een nieuwe apparaatregistratie gemaakt.     

    ![Apparaatregistratie voor IoT hub verwijderen](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Bij de tweede manier moet u ondersteuning voor opnieuw inrichten gebruiken om uw apparaten opnieuw in te richten op dezelfde IoT-hub. Deze aanpak kan worden gebruikt om certificaten te vervangen voor apparaatregistratie op de IoT-hub. Zie [apparaten opnieuw inrichten](how-to-reprovision.md)voor meer informatie.



#### <a name="update-compromised-intermediate-certificates"></a>Veraangetaste tussenliggende certificaten bijwerken

1. Klik op **registratie groepen** en klik vervolgens op de groeps naam in de lijst. 

2. Klik op **tussenliggend certificaat** en **Verwijder het huidige certificaat**. Klik op het mappictogram om naar het nieuwe tussenliggende certificaat te navigeren dat voor de registratie groep moet worden geüpload. Klik op **Opslaan** wanneer u klaar bent. Deze stappen moeten worden uitgevoerd voor het primaire en het secundaire certificaat als beide worden aangetast.

    Dit nieuwe tussenliggende certificaat moet worden ondertekend door een geverifieerd basis-CA-certificaat dat al is toegevoegd aan de inrichtings service. Zie [X. 509-certificaten](concepts-x509-attestation.md#x509-certificates)voor meer informatie.

    ![Afzonderlijke inschrijvingen beheren voor een aangetast tussenliggend](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Zodra het aangetaste certificaat van de inrichtings service is verwijderd, kan het certificaat nog steeds worden gebruikt voor het maken van verbindingen met de IoT-hub zolang er al apparaatregistratie aanwezig zijn. U kunt deze twee manieren verhelpen: 

    De eerste manier is om hand matig naar uw IoT-hub te navigeren en de apparaatregistratie die is gekoppeld aan het aangetaste certificaat, onmiddellijk te verwijderen. Wanneer uw apparaten vervolgens weer worden ingericht met bijgewerkte certificaten, wordt er voor elk apparaat een nieuwe apparaatregistratie gemaakt.     

    ![Apparaatregistratie voor IoT hub verwijderen](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Bij de tweede manier moet u ondersteuning voor opnieuw inrichten gebruiken om uw apparaten opnieuw in te richten op dezelfde IoT-hub. Deze aanpak kan worden gebruikt om certificaten te vervangen voor apparaatregistratie op de IoT-hub. Zie [apparaten opnieuw inrichten](how-to-reprovision.md)voor meer informatie.


## <a name="enrollment-groups-and-certificate-expiration"></a>Inschrijvings groepen en certificaat verlopen

Als u certificaten doorloopt voor het afhandelen van certificaat verlopen, moet u de configuratie van het secundaire certificaat als volgt gebruiken om ervoor te zorgen dat er geen downtime is voor apparaten die u wilt inrichten.

Later wanneer het secundaire certificaat bijna is verlopen en moet worden gedistribueerd, kunt u met de primaire configuratie draaien. Als u op deze manier draait tussen de primaire en secundaire certificaten, zorgt u ervoor dat er geen downtime is voor apparaten die worden ingericht. 

#### <a name="update-expiring-root-ca-certificates"></a>Verlopen basis-CA-certificaten bijwerken

1. Volg de stappen die worden beschreven in [geverifieerde CA-certificaten configureren](how-to-verify-certificates.md) om nieuwe basis-CA-certificaten toe te voegen en te verifiëren.

2. Klik op het tabblad **inschrijvingen beheren** voor uw Device Provisioning service-exemplaar en klik op de lijst **registratie groepen** . Klik op de naam van de registratie groep in de lijst.

3. Klik op **CA-certificaat** en selecteer uw nieuwe basis-CA-certificaat onder de configuratie van het **secundaire certificaat** . Klik vervolgens op **Opslaan**. 

    ![Het nieuwe basis-CA-certificaat voor verloop selecteren](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Later wanneer het primaire certificaat is verlopen, klikt u op het tabblad **certificaten** voor uw Device Provisioning service-exemplaar. Klik op het verlopen certificaat in de lijst en klik vervolgens op de knop **verwijderen** . Bevestig het verwijderen door de naam van het certificaat in te voeren en op **OK** te klikken.

    ![Basis-CA-certificaat verwijderen](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Verlopen tussenliggende certificaten bijwerken


1. Klik op **registratie groepen** en klik op de groeps naam in de lijst. 

2. Klik op **secundair certificaat** en klik vervolgens op het mappictogram om het nieuwe certificaat te selecteren dat u voor de inschrijvings vermelding wilt uploaden. Klik op **Opslaan**.

    Dit nieuwe tussenliggende certificaat moet worden ondertekend door een geverifieerd basis-CA-certificaat dat al is toegevoegd aan de inrichtings service. Zie [X. 509-certificaten](concepts-x509-attestation.md#x509-certificates)voor meer informatie.

   ![Registratie groepen beheren met het secundaire certificaat verloopt](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Later wanneer het primaire certificaat is verlopen, keert u terug en verwijdert u dat primaire certificaat door op de knop **Huidig certificaat verwijderen** te klikken.


## <a name="reprovision-the-device"></a>Het apparaat opnieuw inrichten

Zodra het certificaat op het apparaat en de Device Provisioning-Service is geïmplementeerd, kan het apparaat zelf worden ingericht door contact op te nemen met de Device Provisioning Service. 

Een eenvoudige manier om apparaten opnieuw in te richten, is het Program meren van het apparaat om contact op te nemen met de inrichtings service om door te gaan met de inrichtings stroom als het apparaat een ' ongeautoriseerde ' fout ontvangt van een poging verbinding te maken met de IoT hub.

Een andere manier is dat zowel de oude als de nieuwe certificaten geldig zijn voor een korte overlap ping, en de IoT-hub gebruiken om een opdracht naar apparaten te verzenden, zodat ze zich opnieuw registreren via de inrichtings service om hun IoT Hub verbindings gegevens bij te werken. Omdat elk apparaat opdrachten op verschillende manieren kan verwerken, moet u uw apparaat Program meren om te weten wat er moet gebeuren wanneer de opdracht wordt geactiveerd. Er zijn verschillende manieren waarop u uw apparaat kunt opdracht bieden via IoT Hub en wij raden u aan om met behulp van [directe methoden](../iot-hub/iot-hub-devguide-direct-methods.md) of [taken](../iot-hub/iot-hub-devguide-jobs.md) het proces te initiëren.

Zodra het opnieuw inrichten is voltooid, kunnen apparaten verbinding maken met IoT Hub met behulp van hun nieuwe certificaten.


## <a name="disallow-certificates"></a>Certificaten niet toestaan

Als gevolg van een inbreuk op de beveiliging moet u mogelijk een certificaat voor een apparaat niet toestaan. Als u een certificaat voor een apparaat niet wilt toestaan, schakelt u de inschrijvings vermelding voor het doel apparaat/certificaat uit. Zie voor meer informatie apparaten niet toestaan in het artikel [registratie van uitschrijving beheren](how-to-revoke-device-access-portal.md) .

Wanneer een certificaat is opgenomen als onderdeel van een uitgeschakelde inschrijvings vermelding, zullen pogingen om zich bij een IoT-hub te registreren met die certificaten, mislukken, zelfs als deze is ingeschakeld als onderdeel van een andere inschrijvings vermelding.
 



## <a name="next-steps"></a>Volgende stappen

- Zie [x. 509 Certificate Attestation](concepts-x509-attestation.md) (Engelstalig) voor meer informatie over x. 509-certificaten in de Device Provisioning Service. 
- Zie [certificaten controleren](how-to-verify-certificates.md) voor meer informatie over het gebruik van een bewijs van de 509 voor een X. CA-certificaat met Azure IOT hub Device Provisioning Service.
- Zie [inschrijving van apparaten beheren met Azure Portal](how-to-manage-enrollments.md)voor meer informatie over het gebruik van de portal voor het maken van een registratie groep.