---
title: Een technisch profiel voor een SAML-Uitgever definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel voor een SAML-verlener (Security Assertion Markup Language token) in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f7beccde92030d1e01633f4e4044849d7e91d05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229949"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel voor een SAML-token Uitgever definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) worden verschillende typen beveiligings tokens meegegeven wanneer elke verificatie stroom wordt verwerkt. Een technisch profiel voor een SAML-token uitgever verzendt een SAML-token dat terugkeert naar de Relying Party toepassing (service provider). Normaal gesp roken is dit technische profiel de laatste Orchestration-stap in de gebruikers reis.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `None`. Stel het element **OutputTokenFormat** in `SAML2`op.

In het volgende voor beeld ziet u een `Saml2AssertionIssuer`technisch profiel voor:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Invoer, uitvoer en persistente claims

De **InputClaims**-, **OutputClaims**-en **PersistClaims** -elementen zijn leeg of ontbreken. De **InutputClaimsTransformations** -en **OutputClaimsTransformations** -elementen zijn ook afwezig.

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| IssuerUri | Nee | De naam van de verlener die wordt weer gegeven in het SAML-antwoord. De waarde moet overeenkomen met de naam die is geconfigureerd in de Relying Party-toepassing. |

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het CryptographicKeys-element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| MetadataSigning | Ja | Het x509-certificaat (RSA key set) dat wordt gebruikt voor het ondertekenen van SAML-meta gegevens. Azure AD B2C gebruikt deze sleutel om de meta gegevens te ondertekenen. |
| SamlMessageSigning| Ja| Geef het x509-certificaat (RSA key set) op dat moet worden gebruikt voor het ondertekenen van SAML-berichten. Azure AD B2C gebruikt deze sleutel om het antwoord `<samlp:Response>` te ondertekenen dat naar de Relying Party wordt verzonden.|

## <a name="session-management"></a>Sessiebeheer

Voor het configureren van de Azure AD B2C SAML-sessies tussen een Relying Party toepassing, het `UseTechnicalProfileForSessionManagement` kenmerk van het element, verwijzing naar [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO-sessie.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor een voor beeld van het gebruik van een SAML-Uitgever technisch profiel:

- [Een SAML-toepassing registreren in Azure AD B2C](connect-with-saml-service-providers.md)












