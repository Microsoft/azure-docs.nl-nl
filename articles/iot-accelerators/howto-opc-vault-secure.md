---
title: De communicatie van OPC UA-apparaten met OPC-kluis beveiligen-Azure | Microsoft Docs
description: Het registreren van OPC UA-toepassingen en het uitgeven van ondertekende toepassings certificaten voor uw OPC UA-apparaten met OPC-kluis.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a1b7564988c8a4d63a37b53d18ed3a7359e65d72
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646144"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>De OPC-kluis certificaat beheer service gebruiken

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

In dit artikel wordt uitgelegd hoe u toepassingen registreert en hoe u ondertekende toepassings certificaten uitgeeft voor uw OPC UA-apparaten.

## <a name="prerequisites"></a>Vereisten

### <a name="deploy-the-certificate-management-service"></a>De Certificate Management-service implementeren

Implementeer eerst de service in de Azure-Cloud. Zie [Deploy the OPC kluis Certificate Management service](howto-opc-vault-deploy.md)(Engelstalig) voor meer informatie.

### <a name="create-the-issuer-ca-certificate"></a>Het CA-certificaat van de verlener maken

Als u dit nog niet hebt gedaan, maakt u het CA-certificaat van de certificaat verlener. Zie [het certificaat van de verlener voor OPC-kluis maken en beheren](howto-opc-vault-manage.md)voor meer informatie.

## <a name="secure-opc-ua-applications"></a>Veilige OPC UA-toepassingen

### <a name="step-1-register-your-opc-ua-application"></a>Stap 1: uw OPC UA-toepassing registreren 

> [!IMPORTANT]
> De rol schrijver is vereist voor het registreren van een toepassing.

1. Open uw certificaat service op `https://myResourceGroup-app.azurewebsites.net` en meld u aan.
2. Ga naar **Nieuw registreren**. Voor een toepassings registratie moet aan een gebruiker ten minste de rol van schrijver zijn toegewezen.
2. Het invoer formulier volgt naam conventies in OPC UA. Zo wordt in de volgende scherm afbeelding de instellingen voor het voor beeld van het [OPC UA-referentie server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) in de OPC ua .NET Standard stack weer gegeven:

   ![Scherm afbeelding van registratie van UA-referentie server](media/howto-opc-vault-secure/reference-server-registration.png "Registratie van UA-referentie server")

5. Selecteer **registreren** om de toepassing te registreren in de toepassings database van de certificaat service. De werk stroom leidt de gebruiker rechtstreeks naar de volgende stap om een ondertekend certificaat aan te vragen voor de toepassing.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Stap 2: uw toepassing beveiligen met een door een CA ondertekend toepassings certificaat

Beveilig uw OPC UA-toepassing door een ondertekend certificaat uit te geven op basis van een aanvraag voor certificaat ondertekening (CSR). U kunt ook een nieuw sleutel paar aanvragen, dat een nieuwe persoonlijke sleutel bevat in PFX-of PEM-indeling. Zie de documentatie van uw OPC UA-apparaat voor meer informatie over welke methode voor uw toepassing wordt ondersteund. In het algemeen wordt de CSR-methode aanbevolen, omdat er geen persoonlijke sleutel moet worden overgedragen over een kabel.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Een nieuw certificaat aanvragen met een nieuw sleutel paar

1. Ga naar **toepassingen**.
3. Selecteer een **nieuwe aanvraag** voor een vermelde toepassing.

   ![Scherm afbeelding van nieuw certificaat aanvragen](media/howto-opc-vault-secure/request-new-certificate.png "Nieuw certificaat aanvragen")

3. Selecteer **Nieuw sleutel paar en certificaat aanvragen** om een persoonlijke en een nieuw ondertekend certificaat aan te vragen met de open bare sleutel voor uw toepassing.

   ![Scherm opname van een nieuwe sleutel paar en een nieuw certificaat genereren](media/howto-opc-vault-secure/generate-new-key-pair.png "Nieuw sleutel paar genereren")

4. Vul het formulier in met een onderwerp en de domein namen. Kies voor de persoonlijke sleutel PEM of PFX met wacht woord. Selecteer **Nieuw sleutel paar genereren** om de certificaat aanvraag te maken.

   ![Scherm opname van het scherm Details van certificaat aanvraag weer geven en de knop Nieuw sleutel paar genereren.](media/howto-opc-vault-secure/approve-reject.png "Certificaat goed keuren")

5. Voor goed keuring is een gebruiker met de rol goed keurder en met handtekening machtigingen in Azure Key Vault vereist. In de standaard werk stroom moeten de rollen fiatteur en aanvrager worden toegewezen aan verschillende gebruikers. Selecteer **goed keuren** of **afwijzen** om de daad werkelijke aanmaak van het sleutel paar en de ondertekening bewerking te starten of te annuleren. Het nieuwe sleutel paar wordt gemaakt en veilig opgeslagen in Azure Key Vault, totdat het is gedownload door de aanvrager van het certificaat. Het resulterende certificaat met de open bare sleutel is ondertekend door de CA. Het kan een paar seconden duren voordat deze bewerkingen zijn voltooid.

   ![Scherm opname van Details van certificaat aanvragen weer geven, met goedkeurings bericht onderaan](media/howto-opc-vault-secure/view-key-pair.png "Sleutel paar weer geven")

7. De resulterende persoonlijke sleutel (PFX of PEM) en het certificaat (DER) kunnen hier worden gedownload in de indeling die is geselecteerd als binair bestand downloaden. Er is ook een met base64 gecodeerde versie beschikbaar, bijvoorbeeld om het certificaat te kopiëren en te plakken naar een opdracht regel of tekst invoer. 
8. Nadat de persoonlijke sleutel is gedownload en veilig is opgeslagen, kunt u **persoonlijke sleutel verwijderen** selecteren. Het certificaat met de open bare sleutel blijft beschikbaar voor toekomstig gebruik.
9. Als gevolg van het gebruik van een door een CA ondertekend certificaat, moeten het CA-certificaat en de certificaatintrekkingslijst (CRL) hier ook worden gedownload.

Nu is het afhankelijk van het OPC UA-apparaat hoe het nieuwe sleutel paar moet worden toegepast. Normaal gesp roken worden het CA-certificaat en de CRL gekopieerd naar een `trusted` map, terwijl de open bare en persoonlijke sleutels van het toepassings certificaat worden toegepast op een `own` map in het certificaat archief. Sommige apparaten ondersteunen mogelijk al server push voor certificaat updates. Raadpleeg de documentatie van uw OPC UA-apparaat.

#### <a name="request-a-new-certificate-with-a-csr"></a>Een nieuw certificaat aanvragen bij een CSR 

1. Ga naar **toepassingen**.
3. Selecteer een **nieuwe aanvraag** voor een vermelde toepassing.

   ![Scherm afbeelding van nieuw certificaat aanvragen](media/howto-opc-vault-secure/request-new-certificate.png "Nieuw certificaat aanvragen")

3. Selecteer **Nieuw certificaat aanvragen met handtekening aanvraag** om een nieuw ondertekend certificaat aan te vragen voor uw toepassing.

   ![Scherm opname van een nieuw certificaat genereren](media/howto-opc-vault-secure/generate-new-certificate.png "Nieuw certificaat genereren")

4. Upload CSR door een lokaal bestand te selecteren of door een met base64 gecodeerde CSR in het formulier te plakken. Selecteer **Nieuw certificaat genereren**.

   ![Scherm opname van Details van certificaat aanvraag weer geven](media/howto-opc-vault-secure/approve-reject-csr.png "CSR goed keuren")

5. Voor goed keuring is een gebruiker met de rol goed keurder en met handtekening machtigingen in Azure Key Vault vereist. Selecteer **goed keuren** of **afwijzen** om de werkelijke ondertekening bewerking te starten of te annuleren. Het resulterende certificaat met de open bare sleutel is ondertekend door de CA. Het kan een paar seconden duren voordat deze bewerking is voltooid.

   ![Scherm opname van de details van de certificaat aanvraag weer geven en bevat een goedkeurings bericht onderaan.](media/howto-opc-vault-secure/view-cert-csr.png "Certificaat weer geven")

6. Het resulterende certificaat (DER) kan hier worden gedownload als binair bestand. Er is ook een met base64 gecodeerde versie beschikbaar, bijvoorbeeld om het certificaat te kopiëren en te plakken naar een opdracht regel of tekst invoer. 
10. Nadat het certificaat is gedownload en beveiligd is opgeslagen, kunt u **certificaat verwijderen** selecteren.
11. Als gevolg van het gebruik van een door een CA ondertekend certificaat, moeten het CA-certificaat en de CRL ook hier worden gedownload.

Nu is het afhankelijk van het OPC UA-apparaat hoe het nieuwe certificaat moet worden toegepast. Normaal gesp roken worden het CA-certificaat en de CRL gekopieerd naar een `trusted` map, terwijl het toepassings certificaat wordt toegepast op een `own` map in het certificaat archief. Sommige apparaten ondersteunen mogelijk al server push voor certificaat updates. Raadpleeg de documentatie van uw OPC UA-apparaat.

### <a name="step-3-device-secured"></a>Stap 3: het apparaat is beveiligd

Het OPC UA-apparaat is nu klaar om te communiceren met andere OPC UA-apparaten die zijn beveiligd door door certificerings instanties ondertekende certificaten, zonder verdere configuratie.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de OPC UA-apparaten kunt beveiligen, doet u het volgende:

> [!div class="nextstepaction"]
> [Een Secure Certificate Management-service uitvoeren](howto-opc-vault-secure-ca.md)
