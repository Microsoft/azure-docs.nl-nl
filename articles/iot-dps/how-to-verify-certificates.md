---
title: X. 509 CA-certificaten controleren met Azure IoT Hub Device Provisioning Service
description: De bewijs van het bezit van een X. 509 CA-certificaten met Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0326eef86b42fa8b06c336bbf2b1c6f2f9df0bcf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730351"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Een bewijs van het bezit van de X. 509 CA-certificaten met uw Device Provisioning Service

Een geverifieerde X. 509-certificaat certificerings instantie (CA) is een CA-certificaat dat is geüpload en geregistreerd bij de inrichtings service en is door de controle van het bezit van de service. 

Bewijs-of-eigendom omvat de volgende stappen:
1. Haal een unieke verificatie code op die is gegenereerd door de inrichtings service voor uw X. 509 CA-certificaat. U kunt dit doen vanuit Azure Portal.
2. Maak een X. 509-verificatie certificaat met de verificatie code als onderwerp en onderteken het certificaat met de persoonlijke sleutel die is gekoppeld aan uw X. 509 CA-certificaat.
3. Upload het ondertekende verificatie certificaat naar de service. De service valideert het verificatie certificaat met behulp van het open bare deel van het CA-certificaat dat moet worden geverifieerd, waardoor u in het bezit bent van de persoonlijke sleutel van het CA-certificaat.

Geverifieerde certificaten spelen een belang rijke rol bij het gebruik van inschrijvings groepen. Het verifiëren van het eigendom van het certificaat biedt een extra beveiligingslaag door ervoor te zorgen dat de uploader van het certificaat in bezit is van de persoonlijke sleutel van het certificaat. Verificatie verhindert een kwaad aardige actor waarmee uw verkeer wordt gesniffd en dat certificaat wordt gebruikt om een registratie groep te maken in hun eigen inrichtings service, waardoor uw apparaten effectief worden overgenomen. Door eigenaar te worden van de hoofdmap of een tussenliggend certificaat in een certificaat keten bewijst u dat u gemachtigd bent om blad certificaten te genereren voor de apparaten die worden geregistreerd als onderdeel van die registratie groep. Daarom moet het basis-of tussenliggende certificaat dat is geconfigureerd in een registratie groep een geverifieerd certificaat zijn of moet worden getotaliseerd op een geverifieerd certificaat in de certificaat keten die een apparaat presenteert wanneer het wordt geverifieerd bij de service. Zie [x. 509-certificaten](concepts-x509-attestation.md) en [de toegang tot de inrichtings service beheren met x. 509-certificaten](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)voor meer informatie over x. 509 Certificate Attestation.

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Het open bare deel van een X. 509-certificaat registreren en een verificatie code ophalen

Voer de volgende stappen uit om een CA-certificaat te registreren bij uw inrichtings service en een verificatie code op te halen die u kunt gebruiken tijdens het bewijs van zijn bezit. 

1. Navigeer in het Azure Portal naar uw inrichtings service en open **certificaten** vanuit het menu aan de linkerkant. 
2. Klik op **toevoegen** om een nieuw certificaat toe te voegen.
3. Geef een beschrijvende weergave naam voor het certificaat op. Blader naar het. CER-of. pem-bestand dat het open bare deel van het X. 509-certificaat vertegenwoordigt. Klik op **Uploaden**.
4. Zodra u een melding krijgt dat het certificaat is geüpload, klikt u op **Opslaan**.

    ![Certificaat uploaden](./media/how-to-verify-certificates/add-new-cert.png)  

   Uw certificaat wordt weer gegeven in de lijst **certificaat Verkenner** . Houd er rekening mee dat de **status** van dit certificaat niet is *geverifieerd*.

5. Klik op het certificaat dat u in de vorige stap hebt toegevoegd.

6. Klik in **certificaat Details** op **verificatie code genereren**.

7. De inrichtings service maakt een **verificatie code** die u kunt gebruiken om het eigendom van het certificaat te valideren. Kopieer de code naar het klem bord. 

   ![Certificaat verifiëren](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>De verificatie code digitaal ondertekenen om een verificatie certificaat te maken

Nu moet u de *verificatie code* ondertekenen met de persoonlijke sleutel die is gekoppeld aan uw X. 509 CA-certificaat, waardoor een hand tekening wordt gegenereerd. Dit staat bekend als [bewijs van bezit](https://tools.ietf.org/html/rfc5280#section-3.1) en resulteert in een ondertekend verificatie certificaat.

Micro soft biedt hulpprogram ma's en voor beelden die u kunnen helpen bij het maken van een ondertekend verificatie certificaat: 

- De **Azure IOT hub C SDK** voorziet in Power shell-scripts (Windows) en bash (Linux) om u te helpen bij het maken van ca-en blad certificaten voor ontwikkeling en het uitvoeren van een bewijs van het bezit van een verificatie code. U kunt de [bestanden](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) die relevant zijn voor uw systeem naar een werkmap downloaden en de instructies in het [Leesmij-bestand voor CA-certificaten beheren](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) gebruiken om een CA-certificaat uit te voeren. 
- De **Azure IOT hub C# SDK** bevat het voor beeld van een verificatie van de [groeps certificaat](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), die u kunt gebruiken om een bewijs van zijn of haar eigendom te maken.
 
> [!IMPORTANT]
> Naast het uitvoeren van een bewijs materiaal, kunt u met de Power shell-en bash-scripts die eerder zijn vermeld, basis certificaten, tussenliggende certificaten en blad certificaten maken die kunnen worden gebruikt om apparaten te verifiëren en in te richten. Deze certificaten moeten alleen worden gebruikt voor ontwikkeling. Ze mogen nooit worden gebruikt in een productie omgeving. 

De Power shell-en bash-scripts die zijn opgenomen in de documentatie en Sdk's, zijn afhankelijk van [openssl](https://www.openssl.org/). U kunt ook OpenSSL of andere hulpprogram ma's van derden gebruiken om u te helpen bij het maken van een bewijs van eigendom. Zie [een X. 509-certificaat keten maken](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain)voor een voor beeld van het gebruik van hulp middelen die worden meegeleverd met de sdk's. 


## <a name="upload-the-signed-verification-certificate"></a>Het ondertekende verificatie certificaat uploaden

1. Upload de resulterende hand tekening als een verificatie certificaat naar uw inrichtings service in de portal. Gebruik in **certificaat Details** op het Azure Portal het pictogram  _bestand Verkenner_ naast het veld **verificatie certificaat. pem-of. cer-bestand** om het ondertekende verificatie certificaat van uw systeem te uploaden.

2. Zodra het certificaat is geüpload, klikt u op **verifiëren**. De **status** van uw certificaat wordt **_gecontroleerd_** in de lijst **certificaat Verkenner** . Klik op **vernieuwen** als deze niet automatisch wordt bijgewerkt.

   ![Certificaat verificatie uploaden](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Volgende stappen

- Zie [inschrijving van apparaten beheren met Azure Portal](how-to-manage-enrollments.md)voor meer informatie over het gebruik van de portal voor het maken van een registratie groep.
- Zie [apparaatregistratie beheren met Service-sdk's](./quick-enroll-device-x509-java.md)voor meer informatie over het gebruik van de service-sdk's voor het maken van een registratie groep.