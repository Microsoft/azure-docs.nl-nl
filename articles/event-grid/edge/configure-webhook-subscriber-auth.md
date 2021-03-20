---
title: Webhook-abonnee verificatie configureren-Azure Event Grid IoT Edge | Microsoft Docs
description: Verificatie van webhook-abonnee configureren
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171649"
---
# <a name="configure-webhook-subscriber-authentication"></a>Verificatie van webhook-abonnee configureren

Deze hand leiding bevat voor beelden van de mogelijke configuratie van de webhook-abonnee voor een Event Grid module. Standaard worden alleen HTTPS-eind punten geaccepteerd voor webhook-abonnees. De module Event Grid wordt geweigerd als de abonnee een zelfondertekend certificaat presenteert.

## <a name="allow-only-https-subscriber"></a>Alleen HTTPS-abonnee toestaan

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>HTTPS-abonnee met zelfondertekend certificaat toestaan

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Stel de eigenschap `outbound__webhook__allowUnknownCA` `true` in op alleen in test omgevingen, omdat u meestal zelfondertekende certificaten kunt gebruiken. Voor werk belastingen voor productie raden wij aan dat ze worden ingesteld op **Onwaar**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS-abonnee toestaan, maar certificaat validatie overs Laan

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Stel de eigenschap `outbound__webhook__skipServerCertValidation` `true` alleen in op test omgevingen als u mogelijk geen certificaat presenteert dat moet worden geverifieerd. Voor werk belastingen voor productie raden wij aan dat ze worden ingesteld op **Onwaar** .

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Zowel HTTP als HTTPS toestaan met zelfondertekende certificaten

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Stel de eigenschap `outbound__webhook__httpsOnly` `false` alleen in op test omgevingen als u mogelijk eerst een http-abonnee wilt maken. Voor werk belastingen voor productie raden wij aan dat deze worden ingesteld op **waar**
