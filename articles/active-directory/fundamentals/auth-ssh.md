---
title: SSH-verificatie met Azure Active Directory
description: Architecturale richtlijnen voor het bereiken van SSH-integratie met Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 315fe35a79ade39de9f541504fc2fe52754614de
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749344"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) is een netwerkprotocol dat versleuteling biedt voor het veilig gebruiken van netwerkservices via een niet-beveiligd netwerk. SSH biedt ook een aanmelding via de opdrachtregel, voert externe opdrachten uit en brengt veilig bestanden over. Het wordt vaak gebruikt in UNIX-systemen zoals Linux®. SSH vervangt het Telnet-protocol, dat geen versleuteling biedt in een niet-beveiligde netwerk. 

Azure Active Directory (Azure AD) biedt een extensie voor virtuele machines (VM's) voor linux®-gebaseerde systemen die worden uitgevoerd in Azure. 

## <a name="use-when"></a>Wanneer gebruiken 

* Werken met linux®-VM's waarvoor extern aanmelden is vereist

* Externe opdrachten uitvoeren in Linux®-systemen

* Veilig bestanden overdragen in een niet-beveiligd netwerk

![diagram van Azure AD met SSH-protocol](./media/authentication-patterns/ssh-auth.png)

SSH met Azure AD

## <a name="components-of-system"></a>Onderdelen van het systeem 

* **Gebruiker:** start de SSH-client om een verbinding in te stellen met de virtuele Linux®'s en geeft referenties voor verificatie.

* **Webbrowser:** het onderdeel dat de gebruiker gebruikt. Het communiceert met de id-provider (Azure AD) om de gebruiker veilig te verifiëren en autoriseren.

* **SSH-client:** stuurt het installatieproces van de verbinding aan.

* **Azure AD:** verifieert de identiteit van de gebruiker met behulp van de apparaatstroom en geeft een token uit aan de Virtuele Linux-VM's.

* **Linux-VM:** accepteert het token en biedt een geslaagde verbinding.

## <a name="implement-ssh-with-azure-ad"></a>SSH implementeren met Azure AD 

* [Aanmelden bij een linux®-VM met Azure Active Directory referenties - Azure Virtual Machines ](../../virtual-machines/linux/login-using-aad.md) 

* [OAuth 2.0-apparaatcodestroom - Microsoft Identity Platform ](../develop/v2-oauth2-device-code.md)

* [Integreren met Azure Active Directory (akamai.com)](https://learn.akamai.com/en-us/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

