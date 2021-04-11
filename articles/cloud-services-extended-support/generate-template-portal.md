---
title: ARM-sjabloon genereren voor Cloud Services (uitgebreide ondersteuning) met behulp van de Azure Portal
description: Genereer en down load ARM-sjabloon en parameter bestand voor Cloud Services (uitgebreide ondersteuning) met behulp van de Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077096"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>ARM-sjabloon genereren voor Cloud Services (uitgebreide ondersteuning) met behulp van de Azure Portal

In dit artikel wordt uitgelegd hoe u de ARM-sjabloon en het parameter bestand kunt downloaden van de [Azure Portal](https://portal.azure.com) voor uw Cloud service. De ARM-sjabloon en het parameter bestand kunnen worden gebruikt in implementaties via Power shell om een Cloud service te maken of bij te werken

## <a name="get-arm-template-via-portal"></a>ARM-sjabloon ophalen via de portal

  1. Ga naar de Azure Portal en [Maak een nieuwe Cloud service](deploy-portal.md). Voeg uw Cloud service configuratie, pakket-en definitie bestanden toe. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="Afbeelding toont de sectie uploaden van het tabblad basis beginselen tijdens het maken.":::
  
  2. Zodra alle velden zijn voltooid, gaat u naar het tabblad controleren en maken om de implementatie configuratie te valideren en klikt u op **sjabloon downloaden voor Automation** uw Cloud service (uitgebreide ondersteuning).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="Afbeelding toont het downloaden van de sjabloon onder Cloud service (uitgebreide ondersteuning) op de Azure Portal.":::
  
  3. Down load uw sjabloon-en parameter bestanden. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="Afbeelding toont het downloaden van sjabloon bestand op het Azure Portal.":::
  
  4. Kopieer de SAS-URI van het pakket en de SAS-URI-configuratie op het tabblad controleren en maken en voeg deze toe aan de parameter.jsin het bestand. Deze bestanden kunnen nu worden gebruikt voor het maken van een nieuwe Cloud service via Power shell.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="Installatie kopie toont de SAS URI en configuratie-SAS URI-para meters op de Azure Portal.":::
  
## <a name="next-steps"></a>Volgende stappen 
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md)
  
