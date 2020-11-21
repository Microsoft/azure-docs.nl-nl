---
title: Problemen met het oplossen van certificaten met Azure Stack Edge Pro met GPU | Microsoft Docs
description: Hierin wordt het oplossen van certificaat fouten met Azure Stack Edge Pro GPU-apparaat beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.author: alkohli
ms.openlocfilehash: a4848f5e8b542d80b382abe39f4882a26e0f534c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015677"
---
# <a name="troubleshooting-certificate-errors"></a>Certificaat fouten oplossen

Het artikel bevat oplossingen voor veelvoorkomende certificaat fouten bij het installeren van certificaten op uw Azure Stack Edge Pro-apparaat.

## <a name="common-certificate-errors"></a>Veelvoorkomende certificaat fouten

De volgende tabel bevat algemene certificaat fouten en gedetailleerde informatie over deze fouten en mogelijke oplossingen:

> [!NOTE]
> Exemplaren van &#8220;{0} , {1} ,..., {n} &#8221; positionele para meters aangeven. De positionele para meters hebben waarden, afhankelijk van de certificaten die u gebruikt.

| Foutcode | Description |
|---|---|
| CertificateManagement_UntrustedCertificate | De certificaat keten van het certificaat met de onderwerpnaam {0} is verbroken. Upload het handtekening keten certificaat voordat u dit certificaat uploadt.|
| CertificateManagement_DeviceNotRegistered| Het apparaat is niet geactiveerd. U kunt een ondersteunings certificaat pas na activering uploaden.|
| CertificateManagement_ExpiredCertificate | Het certificaat van het type {0} is binnenkort verlopen of verlopen. Controleer het verlopen van het certificaat en breng zo nodig een nieuw certificaat in.|
| CertificateManagement_FormatMismatch | De certificaat indeling wordt niet ondersteund. Controleer de indeling van het certificaat en breng, indien nodig, een nieuw certificaat in.  Verwacht {0} , gevonden {1} . |
| CertificateManagement_GenericError | Kan de bewerking voor certificaat beheer niet uitvoeren. Voer de bewerking over enkele minuten opnieuw uit. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| CertificateManagement_HttpsBindingFailure | Certificaat met object naam {0} kan geen beveiligd https-kanaal maken. Controleer het certificaat dat u hebt geüpload en breng zo nodig een nieuw certificaat in. Deze fout treedt op bij het knoop punt certificaat van het apparaat.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | Het certificaat met de onderwerpnaam {0} mag geen sleutel gebruiks certificaat ondertekenen. Controleer het sleutel gebruik van het certificaat en breng, indien nodig, een nieuw certificaat in. Deze fout treedt op bij het Ondertekeningscertificaat van de handtekening keten. |
| CertificateManagement_IncorrectKeyNumber | Het certificaat met de onderwerpnaam {0} heeft een onjuist sleutel nummer {1} . Controleer het sleutel nummer van het certificaat en breng, indien nodig, een nieuw certificaat in.|
| CertificateManagement_InvalidP7b | Het geüploade certificaat bestand is niet geldig.  Controleer de indeling van het certificaat en breng, indien nodig, een nieuw certificaat in.|
| CertificateManagement_KeyAlgorithmNotRSA | Dit certificaat maakt geen gebruik van de RSA-sleutel algoritme. Alleen de RSA-certificaten worden ondersteund. |
| CertificateManagement_KeySizeNotSufficient | Het certificaat met de onderwerpnaam {0} heeft onvoldoende sleutel grootte {1} . De minimale sleutel grootte is 4096.|
| CertificateManagement_MissingClientOid | Het certificaat met de onderwerpnaam heeft {0} geen OID voor client verificatie. Controleer de certificaat eigenschappen en breng indien nodig een nieuw certificaat in.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | Het certificaat met de onderwerpnaam heeft {0} geen digitale hand tekening in het sleutel gebruik. Controleer de certificaat eigenschappen en breng indien nodig een nieuw certificaat in. |
| CertificateManagement_MissingKeyCertSignKeyUsage | Het certificaat met de onderwerpnaam heeft {0} geen certificaat ondertekening in het sleutel gebruik. Controleer de certificaat eigenschappen en breng indien nodig een nieuw certificaat in.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | Het certificaat met de onderwerpnaam heeft {0} geen sleutel codering in het sleutel gebruik. Controleer de certificaat eigenschappen en breng indien nodig een nieuw certificaat in. |
| CertificateManagement_MissingServerOid | Het certificaat met de onderwerpnaam {0} heeft geen OID voor Server verificatie. Controleer de certificaat eigenschappen en breng indien nodig een nieuw certificaat in.|
| CertificateManagement_NameMismatch | Het certificaat type komt niet overeen. Verwacht bereik: {0} , gevonden {1} . Upload het juiste certificaat.|
| CertificateManagement_NoPrivateKeyPresent | {0}Er is geen persoonlijke sleutel aanwezig in het certificaat met de onderwerpnaam. Upload een pfx-certificaat met een persoonlijke sleutel.|
| CertificateManagement_NoRSACryptoPrivateKey | De persoonlijke sleutel voor het certificaat met de onderwerpnaam {0} is niet toegankelijk. Zorg ervoor dat u een ondersteund certificaat gebruikt. Alleen de micro soft RSA/SChannel Cryptographic Provider wordt ondersteund. |
| CertificateManagement_NotSelfSignedCertificate | Het certificaat met de onderwerpnaam {0} is niet zelf ondertekend. Basis certificaten moeten zelf worden ondertekend |
| CertificateManagement_NotSupportedOnVirtualAppliance | Deze bewerking wordt niet ondersteund op het virtuele apparaat. Deze fout geeft aan dat ondertekening alleen plaatsvindt bij Data Box Gateway die worden uitgevoerd in een Tactical Cloud-apparaat. Deze fout treedt op tijdens het beheren van het apparaat via Windows Power shell.|
| CertificateManagement_SelfSignedCertificate | Het certificaat met de onderwerpnaam {0} is zelf ondertekend. Upload een certificaat dat juist is ondertekend.|
| CertificateManagement_SignatureAlgorithmSha1 | Het certificaat met de onderwerpnaam {0} bevat een niet-ondersteund handtekening algoritme. SHA1-RSA wordt niet ondersteund. |
| CertificateManagement_SubjectNamesInvalid | Het certificaat met de onderwerpnaam heeft {0} niet de juiste onderwerpnaam of alternatieve onderwerpnaam voor het {1} certificaat. Controleer het certificaat dat u hebt geüpload en breng zo nodig een nieuw certificaat in. Controleer ook of de DNS-naam overeenkomt met de SAN'S namen.|
| CertificateManagement_UnreadableCertificate | Het certificaat met het type {0} kan niet worden gelezen. Deze fout treedt op wanneer het certificaat onleesbaar of beschadigd is. Een nieuw certificaat meenemen.|
| CertificateSubjectNotFound | Kan het certificaat met de onderwerpnaam {0} niet vinden. Een nieuw certificaat meenemen.|
| CertificateRotationGenericFailure | Het draaien van een of meer certificaten is mislukt. Probeer het over enkele minuten opnieuw. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning.|
| CertificateImportFailure | Het certificaat met de vinger afdruk {0} is niet geïmporteerd op het knoop punt {1} . Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| CertificateApplyFailure | Het certificaat met de vinger afdruk {0} is niet toegepast op het knoop punt {1} . Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning.|
| NodeNotReachable | Kan certificaat niet valideren op {0} . Controleer de hardware-en software status van het systeem.|


## <a name="next-steps"></a>Volgende stappen

[Certificaat vereisten](azure-stack-edge-j-series-certificate-requirements.md)