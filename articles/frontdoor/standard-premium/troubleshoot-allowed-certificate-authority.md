---
title: Toegestane certificerings instanties voor Azure front deur Standard/Premium (preview)
description: In dit artikel vindt u een overzicht van alle certificerings instanties die zijn toegestaan wanneer u uw eigen certificaat maakt.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099272"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Toegestane certificerings instanties voor Azure front deur Standard/Premium (preview)

Wanneer u de HTTPS-functie inschakelt met uw eigen certificaat voor een aangepast Azure front deur Standard/Premium-domein. U hebt een toegestane certificerings instantie (CA) nodig om uw TLS/SSL-certificaat te maken. Anders wordt de aanvraag geweigerd als u een niet-toegestane CA of een zelfondertekend certificaat gebruikt.

De volgende Ca's zijn toegestaan wanneer u uw eigen certificaat maakt:

- AddTrust externe CA-basis
- AlphaSSL basis-CA
- AAM infra structuur CA 01
- AAM infra CA 02
- Ameroot
- APCA-DM3P
- Atos TrustedRoot 2011
- Auto Pilot-basis-CA
- Baltimore CyberTrust Root
- Klasse 3 open bare primaire certificerings instantie
- COMODO RSA-certificerings instantie
- COMODO RSA-domein validatie beveiligde server-CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert Global Root G2
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV-basis-CA
- CA voor DigiCert SHA2 uitgebreide validatie server
- DigiCert SHA2 High Assurance Server-CA
- DigiCert SHA2 Secure Server CA
- DST Root CA X3
- D-vertrouwde basis klasse 3 CA 2 2009
- Versleutelen overal DV TLS-CA
- Basis certificerings instantie vertrouwen
- De bevoegdheid basis certificerings instantie-G2
- Entrust.net-certificerings instantie (2048)
- Globale certificerings instantie GeoTrust
- Primaire certificerings instantie voor geovertrouwen
- Primaire certificerings instantie voor geovertrouwen-G2
- GeoTrust RSA CA 2018
- GlobalSign
- CA voor uitgebreide validatie van GlobalSign-SHA256-G2
- CA-GlobalSign organisatie validatie-G2
- GlobalSign basis-CA
- Go Daddy-basis certificerings instantie-G2
- Go Daddy Secure Certificate Authority-G2
- De CA-x3 versleutelen
- Microsec e-Szigno-basis-CA 2009
- QuoVadis root CA2 G3
- RapidSSL RSA CA 2018
- Beveiligings communicatie RootCA1
- Beveiligings communicatie RootCA2
- Beveiligings communicatie RootCA3
- Symantec Class 3 EV SSL CA-G3
- Symantec Class 3 Secure Server CA-G4
- Symantec Enter prise Mobile-hoofdmap voor micro soft
- Thawte primaire basis-CA
- Thawte Primary-basis certificerings instantie-G2
- Thawte Primary-basis certificerings instantie-G3
- Thawte RSA CA 2018
- CA voor Thawte-tijds tempel
- TrustAsia TLS RSA-CA
- SSL-CA voor uitgebreide validatie van VeriSign-klasse 3
- Uitgebreide SSL-certificerings instantie van VeriSign-klasse 3
- VeriSign-klasse 3 open bare primaire certificerings instantie-G5
- CA van VeriSign International Server-klasse 3
- Basis voor de service voor VeriSign-tijds tempels
- Universele basis certificerings instantie voor VeriSign
