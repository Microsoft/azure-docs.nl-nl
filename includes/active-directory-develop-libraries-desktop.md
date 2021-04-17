---
ms.openlocfilehash: 9ddc240257bda29d5db5cae3eb6a830273d339cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107590007"
---
| Taal/framework | Project op<br/>GitHub                                                                                     | Pakket                                                                               | Krijgen<br/>Begon                        | Gebruikers aanmelden                                         | Toegang tot web-API's                                                 | Algemeen beschikbaar (GA) *of*<br/>Openbare preview<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL-Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Openbare preview                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Algemene beschikbaarheid                                                           |
| macOS (Swift/Obj-C)  | [MSAL voor iOS en macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Zelfstudie](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Algemene beschikbaarheid                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Zelfstudie](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Algemene beschikbaarheid                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Zelfstudie](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![Bibliotheek kan id-tokens aanvragen voor aanmelding door gebruikers.][y] | ![Bibliotheek kan toegangstokens aanvragen voor beveiligde web-API's.][y] | Algemene beschikbaarheid                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Aanvullende gebruiksvoorwaarden voor Microsoft Azure zijn][preview-tos] van toepassing op bibliotheken in openbare *preview.*

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/