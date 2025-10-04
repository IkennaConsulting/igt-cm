# REST API Versioning 

Also known as: API evolution, breaking change management.

---

## The Problem
APIs must evolve over time to support new features, fix design issues, and adapt to changing business requirements. However, existing API consumers depend on the current contract and behavior. Making breaking changes without a versioning strategy forces all consumers to update simultaneously or breaks their integrations. Organizations need a way to introduce changes while maintaining backward compatibility for existing consumers and providing a migration path.

---

## Forces
Several competing forces make API evolution challenging:

- **Innovation vs. Stability**: Business needs demand rapid API evolution, but consumers require stable, predictable interfaces they can rely on

- **Provider Efficiency vs. Consumer Convenience**: Maintaining multiple API versions increases operational costs and complexity, but forcing consumers to upgrade creates friction and resistance

- **Technical Debt vs. Forward Progress**: Supporting legacy versions accumulates technical debt, but deprecating too aggressively alienates consumers and damages trust

- **Breaking Changes vs. Backward Compatibility**: Some improvements (better resource modeling, fixing design mistakes) require breaking changes, but backward compatibility is a core REST principle

- **Discovery and Communication**: Consumers need to understand what versions exist, which to use, and when versions will be deprecated—but communicating change effectively is difficult

- **Coordination Complexity**: In distributed systems, multiple teams may consume your API, making synchronized upgrades nearly impossible

- **Semantic Ambiguity**: What constitutes a "breaking change" isn't always clear (Is adding an optional field breaking? What about changing validation rules?)

---

## Risk the Pattern Mitigates

**Integration Breakage Risk**
Without versioning, deploying API changes can immediately break existing consumer applications, causing production outages and service disruptions.

**Consumer Trust Erosion Risk**
Unpredictable API changes damage developer trust and make organizations hesitant to build on your platform, reducing API adoption and ecosystem growth.

**Coordination Bottleneck Risk**
Forcing all consumers to upgrade simultaneously creates coordination bottlenecks, slowing both API evolution and consumer projects, and may be impossible in ecosystems with third-party integrations.

**Technical Debt Accumulation Risk**
Without a clear versioning and deprecation strategy, teams either accumulate cruft from never deprecating old patterns or break consumers by deprecating too aggressively.

**Compliance and Audit Risk**
Versioning provides traceability for API contracts, supporting compliance requirements, SLA agreements, and audit trails for what behavior was available when.

**Documentation and Support Fragmentation Risk**
Without clear versioning, documentation becomes ambiguous, support teams can't determine which API version consumers are using, and troubleshooting becomes difficult.

**Migration Planning Risk**
Consumers can't plan upgrades without knowing what versions exist, what changed, and how long versions will be supported, leading to rushed, error-prone migrations.

---

## How the Solution(s) Work

The API Versioning Pattern provides mechanisms for indicating which version of an API contract a request should use, allowing multiple versions to coexist. There are several implementation strategies:

### Solution Variant 1: URI Path Versioning (Most Common)

Embed the version identifier directly in the URL path:

```
GET /v1/users/123
GET /v2/users/123
GET /v3/users/123
```

**Implementation:**
- Major version number in the path (e.g., `/v1/`, `/v2/`)
- Version applies to all resources under that path segment
- Routes map to different API implementations/handlers

**Advantages:**
- Highly visible and explicit
- Easy to test and route (no header inspection needed)
- Cache-friendly (different URLs = different cache entries)
- Simple for consumers to understand and implement
- Works well with API gateways and proxies

**Disadvantages:**
- Violates REST principle that URIs should identify resources, not versions
- Creates new URLs for the same conceptual resource
- Can lead to URL proliferation

**Best for**: Public APIs, APIs with many external consumers, organizations prioritizing simplicity and discoverability.

### Solution Variant 2: Header Versioning

Use custom headers or standard content negotiation headers:

```
GET /users/123
Accept: application/vnd.company.v2+json

# Or custom header approach:
GET /users/123
API-Version: 2
```

**Implementation:**
- Version specified in `Accept` header (content negotiation)
- Or custom header like `API-Version`, `X-API-Version`
- Server inspects headers and routes accordingly

**Advantages:**
- Resource URIs remain stable across versions
- More RESTful (separates resource identification from representation)
- Supports granular versioning (different resource versions in same request)

**Disadvantages:**
- Less discoverable (not visible in URLs)
- Harder to test in browsers
- More complex routing logic
- Caching requires header-aware configuration
- Consumers may forget to include headers

**Best for**: Internal APIs, APIs consumed by sophisticated clients, teams prioritizing REST purity.

### Solution Variant 3: Query Parameter Versioning

Include version as a query parameter:

```
GET /users/123?version=2
GET /users/123?api-version=2
```

**Implementation:**
- Version specified as query parameter
- Can have default version if parameter omitted

**Advantages:**
- Visible and testable like path versioning
- Doesn't change the base resource URI
- Easy to implement

**Disadvantages:**
- Query parameters traditionally represent filtering/options, not fundamental contract changes
- Can conflict with other query parameters
- Mixes versioning with request parameters
- Less clean than path versioning

**Best for**: APIs where versioning is an occasional need rather than core strategy, backward-compatibility shims.

### Solution Variant 4: Content Negotiation (Media Type Versioning)

Use media types to specify version:

```
GET /users/123
Accept: application/vnd.company.user.v2+json
```

**Implementation:**
- Version embedded in media type
- Follows REST hypermedia principles
- Different representations of the same resource

**Advantages:**
- Most RESTful approach
- Aligns with HTTP standards
- Supports fine-grained versioning per resource type

**Disadvantages:**
- Most complex to implement
- Requires sophisticated consumer understanding
- Media type proliferation
- Harder to discover and test

**Best for**: Mature APIs with hypermedia designs, organizations with strong REST discipline.

### Solution Variant 5: No Versioning (Evolution Strategy)

Make only backward-compatible changes and never version:

**Implementation:**
- Add optional fields, never remove
- Add new endpoints, never change existing
- Use feature flags and capability discovery
- Extensive testing to ensure compatibility

**Advantages:**
- Simplest for consumers (no version management)
- Forces disciplined API design
- Reduces operational complexity

**Disadvantages:**
- Cannot fix design mistakes
- Accumulates cruft over time
- Eventually becomes unsustainable
- Difficult for major architectural changes

**Best for**: Internal microservices, simple APIs, early-stage products where breaking changes are rare.

---

## Versioning Best Practices

**Semantic Versioning Principles:**
- **Major version** (v1, v2): Breaking changes
- **Minor version** (v1.1, v1.2): Backward-compatible additions (optional)
- **Patch version**: Bug fixes (usually not exposed in API versions)

**Versioning Policy Components:**

1. **Breaking Change Definition**: Document what constitutes a breaking change:
   - Removing endpoints, fields, or parameters
   - Renaming fields or changing field types
   - Changing error response structures
   - Modifying authentication/authorization
   - Changing required fields or validation rules

2. **Default Version Strategy**: 
   - Specify default version for requests without version indicator
   - Consider making version specification mandatory to avoid ambiguity

3. **Deprecation Policy**:
   - Minimum support period (e.g., 12-24 months)
   - Deprecation announcement timeline (e.g., 6 months notice)
   - Clear sunset dates for old versions
   - Migration guides and tooling

4. **Version Lifecycle Communication**:
   - Changelog documenting all changes
   - Migration guides between versions
   - Deprecation notices in responses (e.g., `Sunset` header)
   - Email notifications to registered consumers

5. **Parallel Run Period**:
   - Support multiple versions simultaneously
   - Gradual consumer migration
   - Monitor adoption metrics

**Response Headers for Version Management:**

```
Sunset: Sat, 31 Dec 2025 23:59:59 GMT
Deprecation: true
Link: <https://api.example.com/docs/v3/migration>; rel="successor-version"
API-Supported-Versions: 1, 2, 3
```

---

## Contraindications

**Don't use versioning (or use minimally) when:**

- **Internal microservices with coordinated deployment**: If you control both API and all consumers and can deploy them together, versioning adds unnecessary complexity

- **Very early stage products**: Pre-product-market-fit APIs change so rapidly that versioning creates false stability expectations; better to communicate "beta" status

- **Consumers can easily and automatically upgrade**: If you provide SDKs that auto-update or consumers use managed integrations you control

- **APIs are truly CRUD with stable domain models**: Simple CRUD APIs on stable domain entities rarely need versioning

- **Real-time or internal event streams**: Event-driven architectures often use schema evolution strategies (like Avro, Protobuf) rather than API versioning

**Avoid over-versioning:**

- **Don't version for every change**: Only increment major version for breaking changes; use feature flags or additive changes when possible

- **Don't maintain too many versions simultaneously**: Limit to 2-3 active major versions maximum to manage operational complexity

- **Don't version prematurely**: Wait until you actually need to make breaking changes rather than building complex versioning infrastructure speculatively

---

## References

**API Design Standards and Guidelines:**
- [Microsoft REST API Guidelines - Versioning](https://github.com/microsoft/api-guidelines/blob/vNext/graph/GuidelinesGraph.md#versioning-and-deprecation)
- [Google Cloud API Design Guide - Versioning](https://cloud.google.com/apis/design/versioning)
- [Stripe API Versioning Strategy](https://stripe.com/docs/api/versioning) - Excellent real-world example of date-based versioning
- [Zalando RESTful API Guidelines - Compatibility](https://opensource.zalando.com/restful-api-guidelines/#compatibility)
- [GitHub API Versioning](https://docs.github.com/en/rest/overview/api-versions) - Header-based with date versioning

**Books and Deep Dives:**
- "Automating API Delivery: APIOps with OpenAPI" by Ikenna Nwaiwu - Comprehensive versioning coverage


**Standards and Specifications:**
- [RFC 7231 - HTTP Semantics (Content Negotiation)](https://tools.ietf.org/html/rfc7231#section-5.3)
- [RFC 8594 - The Sunset HTTP Header Field](https://tools.ietf.org/html/rfc8594)
- [Semantic Versioning 2.0.0](https://semver.org/)

**Articles and Best Practices:**
- [OpenAPI Specification Versioning](https://spec.openapis.org/oas/latest.html#infoObject) - How to document versions in OpenAPI



