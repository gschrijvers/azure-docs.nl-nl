---
title: SSO en het aanpassen van tokens beheren met aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Meer informatie over het beheren van SSO en het aanpassen van tokens met aangepaste beleids regels in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0b460d5a3c5535b74e349fa46c6a2ad55fc3a8d8
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966569"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>SSO en het aanpassen van tokens beheren met aangepaste beleids regels in Azure Active Directory B2C

In dit artikel vindt u informatie over het beheren van uw token-, sessie-en SSO-configuraties (single sign-on) met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="jtw-token-lifetimes-and-claims-configuration"></a>Levens duur van JTW-token en claims-configuratie

Als u de instellingen voor de levens duur van uw tokens wilt wijzigen, voegt u een [ClaimsProviders](claimsproviders.md) -element toe in het Relying Party bestand van het beleid dat u wilt beïnvloeden.  Het element **ClaimsProviders** is een onderliggend item van het element [TrustFrameworkPolicy](trustframeworkpolicy.md) .

Voeg het ClaimsProviders-element toe tussen het element BasePolicy en het element RelyingParty van het bestand Relying Party.

In moet u de informatie over de levens duur van uw tokens plaatsen. De XML ziet eruit als in dit voor beeld:

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

De volgende waarden zijn ingesteld in het vorige voor beeld:

- **Toegangs token levensduur** : de waarde voor de levens duur van het toegangs token is ingesteld met **token_lifetime_secs** meta gegevens item. De standaard waarde is 3600 seconden (60 minuten).
- **Levens duur van id-token** : de waarde voor de levens duur van het id-token is ingesteld met het **id_token_lifetime_secs** meta gegevens item. De standaard waarde is 3600 seconden (60 minuten).
- **Levens duur van het token vernieuwen** : de waarde voor de levens duur van het vernieuwings token is ingesteld met het **refresh_token_lifetime_secs** meta gegevens item. De standaard waarde is 1209600 seconden (14 dagen).
- De **levens duur van het token sliding window vernieuwen** : als u een sliding window levensduur wilt instellen voor het vernieuwings token, stelt u de waarde van **rolling_refresh_token_lifetime_secs** meta gegevens item in. De standaard waarde is 7776000 (90 dagen). Als u geen sliding window levensduur wilt afdwingen, vervangt u het item door `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` .
- **Claim verlener (ISS)** : de claim verlener (ISS) is ingesteld met het meta gegevens item **IssuanceClaimPattern** . De toepasselijke waarden zijn `AuthorityAndTenantGuid` en `AuthorityWithTfp` .
- **Claim instellen die beleids-id vertegenwoordigt** : de opties voor het instellen van deze waarde zijn `TFP` (vertrouwens raamwerk beleid) en `ACR` (Naslag informatie over de verificatie context). `TFP`is de aanbevolen waarde. Stel **AuthenticationContextReferenceClaimPattern** in met de waarde `None` .

    Voeg dit element toe aan het element **ClaimsSchema** :

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Voeg dit element toe aan het **OutputClaims** -element:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Voor ACR verwijdert u het item **AuthenticationContextReferenceClaimPattern** .

- **Onderwerp (sub) claim** : deze optie wordt standaard ingesteld op ObjectID. Als u deze instelling wilt wijzigen in `Not Supported` , vervangt u deze regel:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    met deze regel:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure AD B2C sessie](session-overview.md).
- Meer informatie over het [configureren van het gedrag van sessies in aangepaste beleids regels](session-behavior-custom-policy.md).
- Verwijzing: [JwtIssuer](jwt-issuer-technical-profile.md).