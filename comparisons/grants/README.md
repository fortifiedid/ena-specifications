# Grant Types Analysis

## Overview

This document analyzes the grant type requirements and restrictions in the Ena OAuth 2.0 specifications compared to established OAuth 2.0 standards.

## Standards Reference
- **RFC 6749**: OAuth 2.0 Authorization Framework (defines core grant types)
- **RFC 7522**: SAML 2.0 Profile for OAuth 2.0 (assertion grant)
- **RFC 7523**: JWT Profile for OAuth 2.0 (assertion grant)
- **OAuth 2.1 Draft**: Next version of OAuth with security improvements

## Grant Type Analysis

### A. Supported Grant Types

#### A.1. Authorization Code Grant (Section 5.1)

**Classification**: **TIGHTENS**

**Standard Behavior**: RFC 6749 Section 4.1 - Core authorization grant

**Profile Requirements**:
- MUST support PKCE (RFC 7636)
- Enhanced redirect URI validation
- Authorization response issuer parameter support
- Extension parameter for authentication provider selection

**Key Enhancements**:
```
Standard: Basic authorization code flow
Profile:  + PKCE mandatory
          + Strict redirect URI validation  
          + Issuer identification in responses
          + Custom authentication provider parameter
```

**Risk Assessment**: **Low**
- PKCE is OAuth 2.1 requirement
- Redirect URI validation prevents attacks
- Extensions maintain compatibility

**Security Benefits**:
1. **PKCE**: Prevents authorization code interception
2. **Redirect Validation**: Prevents redirect attacks
3. **Issuer Parameter**: Prevents mix-up attacks
4. **Authentication Provider**: Supports multi-IdP scenarios

#### A.2. Refresh Token Grant (Section 5.2)

**Classification**: **TIGHTENS**

**Standard Behavior**: RFC 6749 Section 6 - Token refresh mechanism

**Profile Requirements**:
- Mandatory token rotation
- JWT format with encryption recommended
- Session-bound lifecycle
- Mandatory expiration times

**Enhancements**:
```
Standard: Optional refresh token rotation
Profile:  MUST rotate refresh tokens
          SHOULD encrypt refresh tokens
          MUST expire with session
          MUST have time-based expiration
```

**Risk Assessment**: **Low**
- Aligns with OAuth 2.1 draft requirements
- Industry best practice adoption
- Significant security improvement

#### A.3. Client Credentials Grant (Section 5.3)

**Classification**: **TIGHTENS**

**Standard Behavior**: RFC 6749 Section 4.4 - Service-to-service flow

**Profile Requirements**:
- Strong client authentication required
- No user context involved
- Resource parameter support

**Enhancements**:
```
Standard: Any client authentication method
Profile:  Only private_key_jwt or mTLS
```

**Risk Assessment**: **Low**
- Consistent with overall authentication requirements
- Appropriate for machine-to-machine scenarios

#### A.4. SAML Assertion Grant (Section 5.4.1)

**Classification**: **EXTENDS**

**Standard Reference**: RFC 7522

**Profile Requirements**:
- Optional support for SAML-to-OAuth scenarios
- Integration with Swedish eID ecosystem
- Cross-domain authentication support

**Use Cases**:
- Legacy SAML system integration
- Cross-domain authentication
- Enterprise SSO scenarios

**Risk Assessment**: **Low**
- Standard-compliant extension
- Addresses real deployment needs
- Optional implementation

#### A.5. JWT Assertion Grant (Section 5.4.2)

**Classification**: **EXTENDS**

**Standard Reference**: RFC 7523

**Profile Requirements**:
- Optional support for JWT-based assertions
- Cross-domain authentication scenarios
- Token exchange patterns

**Risk Assessment**: **Low**
- Standard-compliant extension
- Enables federation scenarios
- Optional implementation

### B. Prohibited Grant Types (Section 5.5)

#### B.1. Implicit Grant Prohibition

**Classification**: **DIFFERS**

**Standard Behavior**: RFC 6749 Section 4.2 - Defined as valid grant type

**Profile Restriction**: 
```
MUST NOT include 'implicit' in grant_types metadata
```

**Security Justification**:
1. **Front-channel Token Delivery**: Tokens exposed in browser history
2. **No Client Authentication**: Cannot verify client identity
3. **Limited Security Controls**: No PKCE, no refresh tokens
4. **Browser Security**: Vulnerable to XSS and history attacks

**OAuth 2.1 Alignment**: OAuth 2.1 draft also removes implicit grant

**Risk Assessment**: **Low**
- Well-documented security issues
- Industry consensus on deprecation
- Alternative flows available (authorization code + PKCE)

**Migration Path**:
```
From: Implicit grant
To:   Authorization code + PKCE (for SPAs)
```

#### B.2. Resource Owner Password Credentials (ROPC) Prohibition

**Classification**: **DIFFERS**

**Standard Behavior**: RFC 6749 Section 4.3 - Defined as valid grant type

**Profile Restriction**:
```
MUST NOT include 'password' in grant_types metadata
```

**Security Justification**:
1. **Credential Exposure**: Client sees user password
2. **No MFA Support**: Cannot handle multi-factor authentication
3. **Trust Model**: Requires high trust in client application
4. **Phishing Risk**: Users trained to enter credentials in applications

**OAuth 2.1 Alignment**: OAuth 2.1 draft deprecates password grant

**Risk Assessment**: **Low**
- Strong security justification
- Better alternatives available
- Industry best practice

**Migration Path**:
```
From: Password grant
To:   Authorization code flow with proper authentication
```

## Grant Type Security Matrix

| Grant Type | Standard Status | Profile Status | Security Level | Use Case |
|------------|----------------|----------------|----------------|----------|
| Authorization Code | Required | Required + Enhanced | High | Interactive users |
| Refresh Token | Optional | Required + Enhanced | High | Token lifecycle |
| Client Credentials | Required | Required + Enhanced | High | Service-to-service |
| SAML Assertion | Extension | Optional | Medium | Legacy integration |
| JWT Assertion | Extension | Optional | Medium | Federation |
| Implicit | Optional | **Prohibited** | Low | **Deprecated** |
| Password | Optional | **Prohibited** | Low | **Deprecated** |

## Extension Parameters

### A. Authentication Provider Parameter (Section 5.1.1.1)

**Classification**: **EXTENDS**

**Parameter**: `https://id.oidc.se/param/authnProvider`

**Purpose**: 
- Allows client to specify which authentication method to use
- Supports multi-IdP scenarios
- Enables seamless user experience

**Standard Reference**: Swedish OpenID Connect Profile extension

**Risk Assessment**: **Low**
- Regional-specific requirement
- Standards-compliant parameter syntax
- Optional for non-Swedish deployments

**Example Usage**:
```
https://as.example.com/authz?
  client_id=https://client.example.com&
  response_type=code&
  scope=openid&
  redirect_uri=https://client.example.com/callback&
  https://id.oidc.se/param/authnProvider=bank-id
```

## Flow Security Analysis

### A. Authorization Code Flow Enhancements

**Security Improvements**:
1. **PKCE Mandatory**: Prevents code interception attacks
2. **Strict Redirect URIs**: Prevents redirect manipulation
3. **Issuer Parameter**: Prevents AS mix-up attacks
4. **State Parameter**: CSRF protection (implied requirement)

**Attack Surface Reduction**:
- Eliminates implicit grant attack vectors
- Strengthens authorization code protection
- Improves client verification

### B. Refresh Token Flow Enhancements

**Security Improvements**:
1. **Token Rotation**: Prevents refresh token reuse
2. **Encryption**: Protects token content
3. **Session Binding**: Prevents unauthorized refresh
4. **Expiration**: Limits token lifetime

**Operational Benefits**:
- Improved token lifecycle management
- Better session control
- Enhanced audit capabilities

## Implementation Considerations

### A. Client Type Implications

**Confidential Clients Only**:
- Simplifies grant type selection
- Enables strong authentication requirements
- Reduces attack surface

**Public Client Migration**:
```
Public Client Pattern:
- Use backend-for-frontend (BFF) pattern
- Implement authorization code + PKCE via backend
- Maintain confidential client security model
```

### B. Backward Compatibility

**Breaking Changes**:
1. **Implicit Grant Removal**: SPAs need architecture changes
2. **Password Grant Removal**: Direct credential flows prohibited
3. **Strong Authentication**: Shared secret clients need upgrading

**Migration Timeline**:
1. **Phase 1**: Deprecation notice and alternative guidance
2. **Phase 2**: Parallel support with warnings
3. **Phase 3**: Enforcement of restrictions

### C. Tool and Library Support

**Required Capabilities**:
- PKCE support for authorization code flow
- JWT client authentication
- mTLS client authentication
- Resource parameter handling

**Common Libraries Assessment**:
- Most major OAuth libraries support required features
- Some may need configuration for prohibited grants
- JWT client authentication widely supported

## Recommendations

### A. Implementation Priority

**High Priority**:
1. Remove prohibited grant types from configurations
2. Implement PKCE for all authorization code flows
3. Enable refresh token rotation
4. Deploy strong client authentication

**Medium Priority**:
1. Add extension parameter support
2. Implement assertion grant types if needed
3. Enhance redirect URI validation
4. Add issuer parameter support

### B. Migration Strategy

**Immediate Actions**:
1. Audit existing grant type usage
2. Identify implicit/password grant dependencies
3. Plan client authentication upgrades
4. Prepare alternative flow implementations

**Long-term Strategy**:
1. Phase out deprecated grant types
2. Implement security enhancements
3. Monitor for new OAuth developments
4. Regular security review of supported flows

### C. Security Testing

**Test Scenarios**:
1. Verify prohibited grants are rejected
2. Test PKCE enforcement
3. Validate refresh token rotation
4. Confirm client authentication requirements
5. Test redirect URI validation

## Conclusion

The grant type requirements in the Ena OAuth 2.0 profile represent a security-focused approach that eliminates known vulnerable flows while enhancing the security of supported flows.

The prohibition of implicit and password grants aligns with industry best practices and OAuth 2.1 direction. The enhancements to authorization code and refresh token flows provide significant security improvements.

The optional support for assertion grants enables integration scenarios while maintaining security standards.

**Overall Assessment**: The grant type restrictions and enhancements are well-justified, security-focused, and aligned with emerging OAuth standards. The changes improve security posture while maintaining support for legitimate use cases.