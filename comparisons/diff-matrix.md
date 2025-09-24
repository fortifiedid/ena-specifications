# OIDC/OAuth Extensions vs Established Specifications - Comparison Matrix

## Overview

This document provides a comprehensive comparison of the Ena Infrastructure OAuth 2.0 profiles and specifications against established OAuth 2.0, OpenID Connect, and related specifications. Each extension/customization is classified and assessed for interoperability, security implications, and implementation recommendations.

## Classification Criteria

- **DIFFERS**: Breaks or is incompatible with established standards
- **EXTENDS**: Adds extra fields/requirements while maintaining compatibility  
- **TIGHTENS**: Implements stricter requirements than standards require
- **NORMALIZES**: Clarifies or reiterates existing standard behavior

## Risk Assessment

- **Low**: Minor deviation with minimal interop/security impact
- **Medium**: Notable deviation requiring careful implementation consideration
- **High**: Significant deviation that may impact broad interoperability

## Documents Analyzed

### 1. [Ena OAuth 2.0 Interoperability Profile](../ena-oauth2-profile.md)
**Status**: Draft 01 (2025-09-04)  
**Scope**: Core OAuth 2.0 profile for Swedish services

### 2. [Ena OAuth 2.0 User Authentication Best Practices](../ena-oauth2-authn-bp.md)  
**Status**: Draft 01 (2025-09-03)  
**Scope**: Authentication integration patterns

### 3. [Ena OAuth 2.0 Federation Interoperability Profile](../ena-oauth2-federation.md)
**Status**: Draft 01 (2025-03-31)  
**Scope**: Federation patterns using OpenID Federation

---

## Detailed Analysis

### A. Client Requirements and Registration

#### A.1. Client Identifiers (ena-oauth2-profile.md, Section 2.2.1)

**Specification Mapping**: RFC 6749 (OAuth 2.0), RFC 7591 (Dynamic Client Registration)

**Classification**: **TIGHTENS**

**Description**: 
- Requires client identifiers to be globally unique URLs using HTTPS
- Must include host component, no query/fragment components
- Same client_id must be used across multiple authorization servers

**Standard Behavior**: RFC 6749 only requires client identifiers to be unique strings

**Risk Assessment**: **Low**
- Enhances security by preventing confusion attacks
- Aligns with OpenID Federation patterns
- May require changes for existing deployments using non-URL client IDs

**Recommendation**: **Keep as profile** - This tightening improves security and federation compatibility

#### A.2. Client Authentication Methods (ena-oauth2-profile.md, Section 2.2.2.2)

**Specification Mapping**: RFC 7523 (JWT Client Auth), RFC 8705 (mTLS Client Auth)

**Classification**: **TIGHTENS**

**Description**:
- MUST support `private_key_jwt`
- MAY support `tls_client_auth` or `self_signed_tls_client_auth`
- Prohibits `client_secret_basic` and `client_secret_post`

**Standard Behavior**: RFC 6749 allows various client authentication methods including shared secrets

**Risk Assessment**: **Medium**
- Eliminates weaker authentication methods
- Requires PKI infrastructure
- May impact existing deployments using shared secrets

**Recommendation**: **Keep as profile** - Strong security justification, aligns with FAPI requirements

#### A.3. Prohibited Grant Types (ena-oauth2-profile.md, Section 5.5)

**Specification Mapping**: RFC 6749 (OAuth 2.0)

**Classification**: **TIGHTENS**  

**Description**:
- Explicitly prohibits `implicit` and `password` grant types
- Only allows `authorization_code`, `refresh_token`, `client_credentials`, and assertion grants

**Standard Behavior**: RFC 6749 defines these grant types as valid options

**Risk Assessment**: **Low**
- Aligns with OAuth 2.1 and security best practices
- Implicit flow known to have security issues
- Password flow discouraged by security community

**Recommendation**: **Keep as profile** - Well-justified security tightening

### B. Token Handling and Format

#### B.1. JWT Access Tokens (ena-oauth2-profile.md, Section 6.1)

**Specification Mapping**: RFC 6749 (OAuth 2.0), RFC 7519 (JWT)

**Classification**: **TIGHTENS**

**Description**:
- Mandates JWT format for access tokens
- Requires specific claims: `iss`, `sub`, `aud`, `exp`, `iat`, `nbf`, `scope`
- Specifies audience claim behavior

**Standard Behavior**: RFC 6749 treats access tokens as opaque to clients

**Risk Assessment**: **Medium**
- Improves introspection capabilities
- May expose token structure unnecessarily
- Requires careful key management

**Recommendation**: **Keep as profile** - Benefits outweigh risks for federation scenarios

#### B.2. Refresh Token Security (ena-oauth2-profile.md, Section 6.2)

**Specification Mapping**: RFC 6749 (OAuth 2.0)

**Classification**: **TIGHTENS**

**Description**:
- Recommends JWT format with encryption
- Requires rotation of refresh tokens
- Mandates revocation on session end
- Requires expiration times

**Standard Behavior**: RFC 6749 doesn't specify refresh token format or rotation requirements

**Risk Assessment**: **Low**
- Significantly improves security posture
- Aligned with OAuth 2.1 draft
- Standard good practice

**Recommendation**: **Keep as profile** - Security best practice

### C. Authorization Server Requirements

#### C.1. Authorization Server Metadata Extensions (ena-oauth2-profile.md, Section 3.1.1)

**Specification Mapping**: RFC 8414 (Authorization Server Metadata)

**Classification**: **EXTENDS**

**Description**:
- Adds `protected_resources` metadata parameter (RFC 9728)
- Adds `authorization_response_iss_parameter_supported`
- Specifies language tag support for human-readable metadata

**Standard Behavior**: RFC 8414 defines base metadata parameters

**Risk Assessment**: **Low**
- Uses standardized extension parameters
- Maintains backward compatibility
- Improves discoverability

**Recommendation**: **Keep as profile** - Standards-compliant extensions

### D. Security Requirements

#### D.1. Cryptographic Algorithm Requirements (ena-oauth2-profile.md, Section 8.2)

**Specification Mapping**: RFC 7518 (JWA), NIST 800-131A

**Classification**: **TIGHTENS**

**Description**:
- Requires specific algorithms: RS256, ES256 (REQUIRED)
- Recommends additional algorithms: RS384, RS512, ES384, ES512, PS256, PS384, PS512
- Mandates minimum key sizes (RSA 2048-bit, EC 256-bit)
- Prohibits SHA-1

**Standard Behavior**: RFC 7518 has different requirement levels for algorithms

**Risk Assessment**: **Low**
- Aligns with current cryptographic best practices
- Follows NIST guidelines
- Improves security posture

**Recommendation**: **Keep as profile** - Essential for security

#### D.2. Client Authentication JWT Requirements (ena-oauth2-profile.md, Section 8.3.1)

**Specification Mapping**: RFC 7523 (JWT Client Authentication)

**Classification**: **TIGHTENS**

**Description**:
- Requires explicit `typ` header (`client-authentication+jwt`)
- Mandates `kid` parameter when multiple keys
- Requires `jti` claim for replay protection
- Enforces short JWT lifetimes

**Standard Behavior**: RFC 7523 doesn't require explicit typing or replay protection

**Risk Assessment**: **Low**
- Prevents JWT confusion attacks
- Adds replay protection
- Security hardening measures

**Recommendation**: **Keep as profile** - Important security enhancements

### E. Optional Extensions

#### E.1. Resource Parameter (ena-oauth2-profile.md, Section 7.1)

**Specification Mapping**: RFC 8707 (Resource Indicators)

**Classification**: **EXTENDS**

**Description**:
- Recommends support for `resource` parameter
- Adds specific audience restriction requirements
- Defines error handling for unknown resources

**Standard Behavior**: RFC 8707 defines the parameter but doesn't mandate specific behaviors

**Risk Assessment**: **Low**
- Standards-compliant extension
- Improves token scoping
- Maintains compatibility

**Recommendation**: **Keep as profile** - Good practice for multi-resource scenarios

#### E.2. JAR and PAR Support (ena-oauth2-profile.md, Sections 7.2-7.3)

**Specification Mapping**: RFC 9101 (JAR), RFC 9126 (PAR)

**Classification**: **NORMALIZES**

**Description**:
- Provides implementation guidance for JAR and PAR
- Recommends use for high-security deployments
- No additional requirements beyond RFCs

**Standard Behavior**: Same as specified in RFCs

**Risk Assessment**: **Low**
- Pure implementation guidance
- No compatibility issues
- Promotes security best practices

**Recommendation**: **Keep as profile** - Helpful guidance

### F. Authentication Best Practices

#### F.1. Single Sign-On Integration (ena-oauth2-authn-bp.md)

**Specification Mapping**: OAuth 2.0 ecosystem patterns

**Classification**: **NORMALIZES**

**Description**:
- Provides architectural guidance for SSO integration
- Describes patterns for combining authentication and authorization servers
- Documents SAML-to-OAuth integration approaches

**Standard Behavior**: Not covered by core OAuth specifications

**Risk Assessment**: **Low**
- Implementation guidance only
- No protocol modifications
- Common deployment patterns

**Recommendation**: **Keep as profile** - Valuable implementation guidance

### G. Federation Profile

#### G.1. OpenID Federation Integration (ena-oauth2-federation.md)

**Specification Mapping**: OpenID Federation 1.0

**Classification**: **EXTENDS**

**Description**:
- Profiles OAuth 2.0 use within OpenID Federation
- Defines federation-specific metadata requirements
- Specifies trust evaluation processes

**Standard Behavior**: OpenID Federation defines base federation mechanisms

**Risk Assessment**: **Low**
- Builds on established federation standard
- Maintains OAuth 2.0 compatibility
- Addresses specific deployment needs

**Recommendation**: **Keep as profile** - Necessary for federation scenarios

### H. Swedish-Specific Extensions

#### H.1. Language Localization (ena-oauth2-profile.md, Section 2.2.2.6)

**Specification Mapping**: RFC 5646 (Language Tags), RFC 7591 (Client Registration)

**Classification**: **EXTENDS**

**Description**:
- Requires Swedish and English language support
- Uses BCP 47 language tags for client metadata
- Recommends default language versions

**Standard Behavior**: RFC 7591 supports internationalization but doesn't mandate specific languages

**Risk Assessment**: **Low**
- Standards-compliant internationalization
- Regional requirement
- No interoperability impact

**Recommendation**: **Keep as profile** - Regional compliance requirement

#### H.2. Swedish Identity Integration (ena-oauth2-profile.md, Section 6.1)

**Specification Mapping**: OAuth 2.0 token content

**Classification**: **EXTENDS**

**Description**:
- Example includes Swedish personal identity number in tokens
- Provides authentication context information
- Uses standardized claim formats

**Standard Behavior**: OAuth doesn't specify user identity claim formats

**Risk Assessment**: **Low**
- Regional identity requirement
- Uses standard JWT claim patterns
- Optional extension

**Recommendation**: **Keep as profile** - Necessary for Swedish deployments

## Summary of Recommendations

### Quick Wins (Easy Alignment)
1. **Documentation Clarifications**: Ensure all DIFFERS classifications are clearly marked as profile-specific requirements
2. **Reference Updates**: Add explicit references to newer OAuth 2.1 work where applicable
3. **Example Updates**: Provide both standard and profile-compliant examples where they differ

### Strategic Profile Decisions

#### Keep as Profile (Recommended)
- Client identifier URL requirements
- Strong client authentication requirements  
- JWT access token format
- Cryptographic algorithm requirements
- Prohibited grant types
- Refresh token security enhancements

#### Consider for Standardization
- Resource parameter usage patterns
- Client authentication JWT typing requirements
- Language localization patterns

### Implementation Considerations

1. **Interoperability**: Most requirements are tightenings that improve security without breaking compatibility
2. **Migration Path**: Provide clear migration guidance for existing deployments
3. **Testing**: Develop conformance test suite for profile compliance
4. **Tool Support**: Ensure major OAuth libraries can support profile requirements

### Risk Mitigation

1. **Medium Risk Items**: Provide detailed implementation guidance and examples
2. **Legacy Support**: Consider transition periods for breaking changes
3. **Vendor Engagement**: Work with OAuth tool vendors to ensure support

## Conclusion

The Ena OAuth 2.0 profiles represent a well-considered set of security hardenings and interoperability improvements over base OAuth 2.0. The majority of requirements are **TIGHTENS** or **EXTENDS** classifications that improve security without breaking fundamental compatibility.

The profile successfully balances security requirements with practical deployability, and the Swedish-specific requirements are appropriately scoped to regional needs without impacting broader interoperability.

**Overall Assessment**: The profiles should be maintained as they provide significant security value while remaining largely compatible with the OAuth 2.0 ecosystem.