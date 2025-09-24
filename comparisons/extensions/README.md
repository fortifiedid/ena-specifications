# OAuth 2.0 Extensions Analysis

## Overview

This document analyzes the OAuth 2.0 extensions profiled in the Ena specifications, comparing their implementation with established standards and assessing their interoperability implications.

## Extension Categories

### A. Standardized Extensions (RFC-based)
- Resource Indicators (RFC 8707)
- JWT-Secured Authorization Requests - JAR (RFC 9101)
- Pushed Authorization Requests - PAR (RFC 9126)
- DPoP - Demonstrating Proof of Possession (RFC 9449)
- Mutual TLS (RFC 8705)

### B. Profile-Specific Extensions
- Swedish authentication provider parameter
- Language localization requirements
- Protected resources metadata

### C. Emerging Standards
- Authorization Server Issuer Identification (draft-ietf-oauth-iss-auth-resp)

## Detailed Extension Analysis

### A. Resource Indicators (RFC 8707) - Section 7.1

**Classification**: **EXTENDS**

**Standard Definition**: RFC 8707 defines the `resource` parameter for indicating target protected resources

**Profile Requirements**:
```
RECOMMENDED: Support and use of resource parameter
MUST: Use Resource Identifier as parameter value
MUST: Reject unmappable resource parameters with invalid_target
RECOMMENDED: Single resource parameter per access token request
MUST: Audience-restrict tokens based on resource parameter
```

**Standard vs Profile**:
```
RFC 8707        | Profile Enhancement
Basic parameter | + Resource Identifier mapping
Error handling  | + Specific error codes (invalid_target)
Multi-resource  | + Single resource recommendation
Token audience  | + Mandatory audience restriction
```

**Risk Assessment**: **Low**
- Standards-compliant enhancement
- Improves token scoping and security
- Maintains backward compatibility
- Supports multi-resource deployments

**Interoperability Impact**:
- **Positive**: Better token scoping reduces over-privileging
- **Positive**: Clear error handling improves client behavior
- **Neutral**: Optional extension, backward compatible

**Implementation Considerations**:
- Requires resource identifier registry
- Need clear mapping between resources and identifiers
- Authorization server must validate resource access

**Example Usage**:
```http
POST /token HTTP/1.1
Host: as.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&
code=SplxlOBeZQQYbYS6WxSbIA&
redirect_uri=https://client.example.com/callback&
resource=https://api.example.com&
client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer&
client_assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
```

### B. JWT-Secured Authorization Requests - JAR (RFC 9101) - Section 7.2

**Classification**: **NORMALIZES**

**Standard Definition**: RFC 9101 enables OAuth authorization requests to be passed as signed JWTs

**Profile Guidance**:
```
OPTIONAL: Authorization server support
SHOULD: Support for OpenID Provider deployments
RECOMMENDED: High-security deployments should use JAR or PAR
```

**Profile Position**:
- Pure implementation guidance
- No additional requirements beyond RFC 9101
- Promotes security best practices
- Addresses request integrity and confidentiality

**Risk Assessment**: **Low**
- No compatibility concerns
- Optional implementation
- Standards-compliant guidance
- Improves security for participating parties

**Security Benefits**:
1. **Request Integrity**: Prevents parameter tampering
2. **Request Confidentiality**: Encrypts sensitive parameters
3. **Large Requests**: Overcomes URL length limitations
4. **Replay Protection**: JWT mechanisms apply

**Example Request Object**:
```json
{
  "iss": "https://client.example.com",
  "aud": "https://as.example.com",
  "response_type": "code",
  "client_id": "https://client.example.com",
  "redirect_uri": "https://client.example.com/callback",
  "scope": "openid profile",
  "state": "af0ifjsldkj",
  "exp": 1759654860,
  "iat": 1759654800
}
```

### C. Pushed Authorization Requests - PAR (RFC 9126) - Section 7.3

**Classification**: **NORMALIZES**

**Standard Definition**: RFC 9126 enables clients to push authorization parameters via back-channel

**Profile Guidance**:
```
RECOMMENDED: High-security deployments should use PAR or JAR
No additional requirements beyond RFC 9126
```

**Security Benefits**:
1. **Parameter Confidentiality**: Removes sensitive data from front-channel
2. **Request Integrity**: Direct client-to-AS transmission
3. **Client Authentication**: Bound to client identity
4. **Large Requests**: Supports complex authorization requests

**Risk Assessment**: **Low**
- Pure implementation guidance
- No interoperability concerns
- Optional extension
- Strong security benefits

**Flow Example**:
```
1. Client POSTs parameters to PAR endpoint
   POST /par HTTP/1.1
   Host: as.example.com
   
2. AS returns request_uri
   {"request_uri": "urn:example:bwc4JK-ESC0w8acc191e-Y1LTC2", "expires_in": 60}
   
3. Client uses request_uri in authorization request
   https://as.example.com/authz?client_id=client&request_uri=urn:example:bwc4JK-ESC0w8acc191e-Y1LTC2
```

### D. DPoP - Demonstrating Proof of Possession (RFC 9449)

**Classification**: **EXTENDS**

**Standard Definition**: RFC 9449 provides sender-constrained access tokens using proof-of-possession

**Profile Requirements**:
```
Metadata parameter: dpop_bound_access_tokens
Client registration: MUST set to true if always using DPoP
Sender constraint: Recommended for high-security deployments
```

**Standard vs Profile**:
```
RFC 9449        | Profile Addition
Basic DPoP      | + Registration metadata parameter
Optional use    | + Deployment recommendations
```

**Risk Assessment**: **Low**
- Standards-compliant metadata extension
- Addresses token theft scenarios
- Optional implementation
- Strong security benefits

**Security Benefits**:
1. **Token Binding**: Prevents token theft and replay
2. **Proof of Possession**: Cryptographic binding to client
3. **Replay Protection**: Nonce and timestamp mechanisms
4. **Man-in-the-Middle Protection**: Public key binding

**DPoP Proof Example**:
```json
{
  "typ": "dpop+jwt",
  "alg": "ES256",
  "jwk": {...}
}
{
  "jti": "e1j3V_bX2U-3NsmAT63kgw",
  "htm": "GET",
  "htu": "https://resource.example.org/protectedresource",
  "iat": 1562262616,
  "ath": "fUHyO2r2Z3DZ53EsNrWBb0xWXoaNy59IiKCAqksmQEo"
}
```

### E. Mutual TLS (RFC 8705) - Section 8.3.2

**Classification**: **EXTENDS**

**Standard Definition**: RFC 8705 defines mTLS for client authentication and certificate-bound tokens

**Profile Requirements**:
```
MAY support: tls_client_auth and self_signed_tls_client_auth
SHOULD: Limit to single root CA
RECOMMENDED: Use mTLS endpoint aliases
Metadata parameter: tls_client_certificate_bound_access_tokens
```

**Profile Enhancements**:
```
RFC 8705           | Profile Addition
Basic mTLS auth    | + Single root CA recommendation
Endpoint support   | + mTLS alias recommendation
Token binding      | + Metadata parameter requirements
```

**Risk Assessment**: **Low**
- Strengthens the already strong RFC 8705 requirements
- Trust anchor limitation improves security
- Endpoint alias recommendation improves deployment

**Security Benefits**:
1. **Strong Client Authentication**: Certificate-based identity
2. **Token Binding**: Prevents token theft
3. **PKI Integration**: Leverages existing certificate infrastructure
4. **Non-repudiation**: Cryptographic proof of client identity

### F. Profile-Specific Extensions

#### F.1. Swedish Authentication Provider Parameter

**Classification**: **EXTENDS**

**Parameter**: `https://id.oidc.se/param/authnProvider`

**Purpose**:
- Enable multi-IdP selection
- Support Swedish eID ecosystem
- Maintain user experience continuity

**Standard Reference**: Swedish OpenID Connect Profile

**Risk Assessment**: **Low**
- Regional-specific requirement
- Standards-compliant parameter naming
- No impact on non-Swedish deployments

**Example Values**:
```
bank-id          - Swedish BankID
freja-id         - Freja eID
mobile-bankid    - Mobile BankID
```

#### F.2. Language Localization (Section 2.2.2.6)

**Classification**: **EXTENDS**

**Requirements**:
```
SHOULD: Provide metadata in Swedish and English
SHOULD: Use BCP 47 language tags
RECOMMENDED: Include default language version
```

**Standard Reference**: RFC 5646 (Language Tags)

**Example Implementation**:
```json
{
  "client_name": "Example Client",
  "client_name#sv": "Exempelklient", 
  "client_name#en": "Example Client",
  "policy_uri": "https://example.com/policy",
  "policy_uri#sv": "https://example.com/policy/sv",
  "policy_uri#en": "https://example.com/policy"
}
```

**Risk Assessment**: **Low**
- Standards-compliant internationalization
- Regional compliance requirement
- No interoperability impact

#### F.3. Protected Resources Metadata (Section 3.1.1.3)

**Classification**: **EXTENDS**

**Parameter**: `protected_resources`

**Standard Reference**: RFC 9728 (Resource Indicators)

**Purpose**:
- Advertise supported protected resources
- Enable automatic resource discovery
- Support resource parameter validation

**Risk Assessment**: **Low**
- Uses standardized RFC 9728 parameter
- Improves discoverability
- Optional extension

## Extension Deployment Matrix

| Extension | Standard | Profile Status | Security Impact | Deployment Complexity |
|-----------|----------|----------------|-----------------|---------------------|
| Resource Indicators | RFC 8707 | Enhanced | Medium+ | Low |
| JAR | RFC 9101 | Guidance | High | Medium |
| PAR | RFC 9126 | Guidance | High | Medium |
| DPoP | RFC 9449 | Metadata | High | Medium |
| mTLS | RFC 8705 | Enhanced | High | High |
| Auth Provider | Regional | Required* | Low | Low |
| Localization | RFC 5646 | Regional | None | Low |
| Protected Resources | RFC 9728 | Optional | Low | Low |

*Required for Swedish deployments

## Implementation Recommendations

### A. Priority Classification

**High Priority (Security-Critical)**:
1. **Resource Indicators**: Improves token scoping
2. **DPoP**: Addresses token theft
3. **mTLS**: Strong authentication and token binding

**Medium Priority (Best Practices)**:
1. **JAR/PAR**: Request integrity and confidentiality
2. **Protected Resources**: Discoverability

**Low Priority (Regional/Optional)**:
1. **Authentication Provider**: Swedish-specific
2. **Localization**: Regional compliance

### B. Deployment Strategy

**Phase 1 - Foundation**:
- Implement Resource Indicators support
- Add protected resources metadata
- Enable language localization if needed

**Phase 2 - Security Enhancement**:
- Deploy DPoP for high-security scenarios
- Implement mTLS for strong authentication
- Consider JAR/PAR for sensitive requests

**Phase 3 - Regional Compliance**:
- Add authentication provider parameter support
- Implement Swedish-specific requirements

### C. Interoperability Testing

**Test Scenarios**:
1. **Resource Parameter**: Validate audience restriction
2. **DPoP**: Test token binding and proof validation
3. **mTLS**: Certificate validation and token binding
4. **JAR/PAR**: Request object processing
5. **Metadata**: Extension parameter handling

## Risk Assessment Summary

### Low Risk Extensions
- All analyzed extensions fall into low risk category
- Standards-compliant implementations
- Optional or backward-compatible
- Clear security benefits

### Mitigation Strategies
1. **Implementation Testing**: Thorough testing with major OAuth libraries
2. **Documentation**: Clear implementation guidance
3. **Migration Support**: Backward compatibility during transitions
4. **Monitoring**: Track adoption and issues

## Conclusion

The extension strategy in the Ena OAuth 2.0 profiles demonstrates a mature approach to OAuth ecosystem enhancement:

1. **Standards Alignment**: All extensions are based on established or emerging RFCs
2. **Security Focus**: Extensions primarily enhance security posture
3. **Optional Implementation**: Most extensions are optional, preserving compatibility
4. **Regional Adaptation**: Swedish-specific requirements are properly scoped

The combination of standardized security extensions (DPoP, mTLS) with practical enhancements (Resource Indicators) creates a robust foundation for high-security OAuth deployments while maintaining interoperability with the broader OAuth ecosystem.

**Overall Assessment**: The extension profile is well-designed, security-focused, and maintains strong alignment with OAuth standards while addressing real-world deployment needs.