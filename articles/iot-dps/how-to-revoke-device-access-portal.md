---
title: Het apparaat uitschrijven vanuit Azure IoT Hub Device Provisioning Service
description: Het registreren van een apparaat om het inrichten via Azure IoT Hub Device Provisioning Service (DPS) te voor komen
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 120446472038076e34f62b47ba79348e5de8b972
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951074"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Het registreren van een apparaat bij Azure IoT Hub Device Provisioning Service

Het juiste beheer van systeemreferenties is essentieel voor systemen met een hoog profiel, zoals IoT-oplossingen. Een best practice voor dergelijke systemen is een duidelijk plan te hebben voor het intrekken van de toegang voor apparaten wanneer hun referenties, of een SAS-token (Shared Access signatures) of een X. 509-certificaat, kan worden aangetast. 

Door registratie in de Device Provisioning Service kan een apparaat worden [ingericht](about-iot-dps.md#provisioning-process). Een ingericht apparaat is geregistreerd bij IoT Hub, waardoor het de eerste dubbele toestand van het [apparaat](~/articles/iot-hub/iot-hub-devguide-device-twins.md) kan ontvangen en telemetrie-gegevens kan rapporteren. In dit artikel wordt beschreven hoe u een apparaat uit het inrichtings service-exemplaar uitschrijft, waardoor het niet meer in de toekomst kan worden ingericht.

> [!NOTE] 
> Houd rekening met het beleid voor opnieuw proberen van apparaten waarvoor u de toegang intrekt. Een apparaat met een oneindig beleid voor opnieuw proberen kan zich bijvoorbeeld voortdurend registreren bij de inrichtings service. Deze situatie verbruikt service bronnen en is mogelijk van invloed op de prestaties.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Apparaten niet toestaan met een afzonderlijke inschrijvings vermelding

Afzonderlijke registraties zijn van toepassing op één apparaat en kunnen X. 509-certificaten of SAS-tokens (in een echte of virtuele TPM) gebruiken als Attestation-mechanisme. (Apparaten die SAS-tokens gebruiken als Attestation-mechanisme kunnen alleen worden ingericht via een individuele inschrijving.) Als u een apparaat met een afzonderlijke inschrijving niet wilt toestaan, kunt u de inschrijvings vermelding uitschakelen of verwijderen. 

Het apparaat tijdelijk niet toestaan door de inschrijvings vermelding uit te scha kelen: 

1. Meld u aan bij de Azure Portal en selecteer **alle resources** in het menu links.
2. Selecteer in de lijst met resources de inrichtings service waarvan u het apparaat niet wilt toestaan.
3. Selecteer in uw inrichtings service **inschrijvingen beheren** en selecteer vervolgens het tabblad **afzonderlijke registraties** .
4. Selecteer de inschrijvings vermelding voor het apparaat dat u niet wilt toestaan. 

    ![Selecteer uw individuele inschrijving](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Schuif op de pagina inschrijving naar beneden en selecteer **uitschakelen** voor de switch **invoer inschakelen** en selecteer vervolgens **Opslaan**.  

   ![Individuele inschrijvings vermelding uitschakelen in de portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Het apparaat permanent niet toestaan door de inschrijvings vermelding te verwijderen:

1. Meld u aan bij de Azure Portal en selecteer **alle resources** in het menu links.
2. Selecteer in de lijst met resources de inrichtings service waarvan u het apparaat niet wilt toestaan.
3. Selecteer in uw inrichtings service **inschrijvingen beheren** en selecteer vervolgens het tabblad **afzonderlijke registraties** .
4. Schakel het selectie vakje in naast de inschrijvings vermelding voor het apparaat dat u niet wilt toestaan. 
5. Selecteer boven aan het venster **verwijderen** en selecteer vervolgens **Ja** om te bevestigen dat u de registratie wilt verwijderen. 

   ![Afzonderlijke inschrijvings vermelding verwijderen in de portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Nadat u de procedure hebt voltooid, ziet u dat uw vermelding is verwijderd uit de lijst met afzonderlijke registraties.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Een X. 509-tussenliggend of basis-CA-certificaat niet toestaan met behulp van een registratie groep

X. 509-certificaten worden meestal gerangschikt in een vertrouwens keten van certificaten. Als een certificaat in een wille keurig stadium in een keten wordt aangetast, wordt de vertrouwens relatie verbroken. Het certificaat mag niet worden toegestaan om te voor komen dat Device Provisioning Service in een keten met dat certificaat wordt ingericht. Zie [x. 509-certificaten](./concepts-x509-attestation.md#x509-certificates)voor meer informatie over x. 509-certificaten en hoe deze worden gebruikt in combi natie met de inrichtings service. 

Een registratie groep is een vermelding voor apparaten die een gemeen schappelijk Attestation-mechanisme van X. 509-certificaten delen die zijn ondertekend door dezelfde tussenliggende of basis-CA. De vermelding van de registratie groep is geconfigureerd met het X. 509-certificaat dat is gekoppeld aan de tussenliggende of basis-CA. De vermelding wordt ook geconfigureerd met configuratie waarden, zoals een dubbele status-en IoT hub-verbinding, die worden gedeeld door apparaten met dat certificaat in de certificaat keten. Als u het certificaat niet wilt toestaan, kunt u de bijbehorende registratie groep uitschakelen of verwijderen.

Het certificaat tijdelijk niet toestaan door de registratie groep uit te scha kelen: 

1. Meld u aan bij de Azure Portal en selecteer **alle resources** in het menu links.
2. Selecteer in de lijst met resources de inrichtings service waarvan u het handtekening certificaat niet wilt toestaan.
3. Selecteer in uw inrichtings service **inschrijvingen beheren** en selecteer vervolgens het tabblad **inschrijvings groepen** .
4. Selecteer de registratie groep met het certificaat dat u niet wilt toestaan.
5. Selecteer **uitschakelen** op de schakel optie **invoer inschakelen** en selecteer vervolgens **Opslaan**.  

   ![Invoer voor registratie groep uitschakelen in de portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Het certificaat permanent niet toestaan door de registratie groep te verwijderen:

1. Meld u aan bij de Azure Portal en selecteer **alle resources** in het menu links.
2. Selecteer in de lijst met resources de inrichtings service waarvan u het apparaat niet wilt toestaan.
3. Selecteer in uw inrichtings service **inschrijvingen beheren** en selecteer vervolgens het tabblad **inschrijvings groepen** .
4. Schakel het selectie vakje in naast de registratie groep voor het certificaat dat u wilt niet toestaan. 
5. Selecteer boven aan het venster **verwijderen** en selecteer vervolgens **Ja** om te bevestigen dat u de registratie groep wilt verwijderen. 

   ![Vermelding voor registratie groep verwijderen in de portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Nadat u de procedure hebt voltooid, ziet u dat uw vermelding is verwijderd uit de lijst met registratie groepen.  

> [!NOTE]
> Als u een registratie groep voor een certificaat verwijdert, kunnen apparaten met het certificaat in de certificaat keten nog steeds worden inge schreven als er een ingeschakelde inschrijvings groep voor het basis certificaat of een ander tussenliggend certificaat hoger in de certificaat keten bestaat.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Specifieke apparaten in een registratie groep niet toestaan

Voor apparaten die het X. 509 Attestation-mechanisme implementeren, worden de certificaat keten en de persoonlijke sleutel van het apparaat gebruikt voor verificatie. Wanneer een apparaat verbinding maakt en zich verifieert met Device Provisioning Service, zoekt de service eerst naar een afzonderlijke inschrijving die overeenkomt met de referenties van het apparaat. De service zoekt vervolgens de registratie groepen om te bepalen of het apparaat kan worden ingericht. Als de service een uitgeschakelde individuele inschrijving voor het apparaat vindt, wordt voor komen dat het apparaat verbinding maakt. De service voor komt de verbinding zelfs als een ingeschakelde registratie groep voor een tussenliggende of basis-CA in de certificaat keten van het apparaat bestaat. 

Voer de volgende stappen uit om een afzonderlijk apparaat in een registratie groep niet toe te staan:

1. Meld u aan bij de Azure Portal en selecteer **alle resources** in het menu links.
2. Selecteer in de lijst met resources de inrichtings service die de registratie groep bevat voor het apparaat dat u niet wilt toestaan.
3. Selecteer in uw inrichtings service **inschrijvingen beheren** en selecteer vervolgens het tabblad **afzonderlijke registraties** .
4. Selecteer bovenaan de knop **afzonderlijke registratie toevoegen** . 
5. Selecteer op de pagina **inschrijving toevoegen** de optie **X. 509** als het Attestation- **mechanisme** voor het apparaat.

    Upload het certificaat van het apparaat en voer de apparaat-ID van het apparaat in dat niet mag worden toegestaan. Gebruik voor het certificaat het ondertekende eind entiteit certificaat dat op het apparaat is geïnstalleerd. Het apparaat gebruikt het ondertekende eind entiteit certificaat voor authenticatie.

    ![Apparaateigenschappen instellen voor het niet-toegestane apparaat](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Ga naar de onderkant van de pagina **registratie toevoegen** en selecteer **uitschakelen** op de schakel optie **invoer inschakelen** en selecteer vervolgens **Opslaan**. 

    [![Uitgeschakelde individuele inschrijvings vermelding gebruiken om het apparaat uit te scha kelen voor het inschrijven van de groep in de portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Wanneer u uw inschrijving hebt gemaakt, moet u de registratie van uitgeschakelde apparaten weer geven op het tabblad **afzonderlijke registraties** . 

## <a name="next-steps"></a>Volgende stappen

Registratie maakt ook deel uit van het grotere proces voor het ongedaan maken van de inrichting. Het ongedaan maken van de inrichting van een apparaat omvat zowel de inschrijving van de inrichtings service als de registratie van IoT hub. Zie voor meer informatie over het volledige proces de [inrichting van apparaten die eerder automatisch zijn ingericht](how-to-unprovision-devices.md)