# Shared Component Library Pattern

## The Problem
Organizations with multiple APIs face the challenge of defining and maintaining common data structures, parameters, response formats, security schemes, and error models consistently across their API portfolio. Without a systematic approach, teams repeatedly define similar or identical elements in each API specification, leading to inconsistency, duplication, and maintenance overhead as the API portfolio grows.

---

## Forces
Several competing forces make this problem difficult to solve:

- **Autonomy vs. Consistency**: Development teams want independence to move quickly, but the organization needs consistency across APIs for a coherent developer experience

- **Speed vs. Quality**: Teams are pressured to deliver APIs rapidly, making it tempting to skip the extra effort of extracting and referencing shared components

- **Local Optimization vs. Enterprise View**: What's fastest for a single API project (copying definitions) creates long-term technical debt across the portfolio

- **Versioning Complexity**: Shared components need versioning strategies that don't break existing API consumers while allowing evolution

- **Ownership Ambiguity**: It's unclear who owns, maintains, and approves changes to shared components versus API-specific definitions

- **Discoverability**: Teams may not know what reusable components already exist, leading them to recreate similar definitions

- **Tooling Maturity**: Not all OpenAPI tools handle `$ref` references equally well, especially across file boundaries

---

## Risk the Pattern Mitigates

**API Inconsistency Risk**
Without shared components, similar concepts (like error responses, pagination, or customer objects) are defined differently across APIs, creating confusion and integration friction for consumers.

**Maintenance Burden Risk**
Duplicated definitions across multiple specifications multiply maintenance effort. A single change (like adding GDPR fields to a person schema) requires updates in dozens of places, increasing the likelihood of errors and omissions.

**Compliance and Security Risk**
Critical security schemes, audit fields, or regulatory requirements defined inconsistently across APIs create compliance gaps and security vulnerabilities that are hard to detect and remediate.

**Quality Degradation Risk**
Without shared, reviewed components, quality varies based on individual developer expertise. Teams may implement suboptimal patterns or miss important validations.

**Onboarding and Cognitive Load Risk**
API consumers must learn different patterns for each API rather than leveraging knowledge across the portfolio, slowing adoption and increasing support costs.

**Documentation Fragmentation Risk**
Inconsistent definitions lead to inconsistent documentation, making it harder to create unified developer portals and API catalogs.

---

## How the Solution(s) Work

The Shared Component Library Pattern establishes reusable OpenAPI components that multiple API specifications reference rather than redefine. There are several implementation variants:

### Solution Variant 1: Internal Components Section (Basic)
Within a single OpenAPI specification, define reusable elements in the `components` section and reference them using `$ref`:

```yaml
components:
  schemas:
    Error:
      type: object
      properties:
        code: string
        message: string
    
paths:
  /users:
    get:
      responses:
        '404':
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
```

**Best for**: Single API specifications that need internal consistency.

### Solution Variant 2: External Component Files (Intermediate)
Extract common components into separate YAML/JSON files that multiple API specifications reference:

```yaml
# common-components.yaml
components:
  schemas:
    StandardError:
      type: object
      # definition...

# user-api.yaml
paths:
  /users:
    get:
      responses:
        '404':
          content:
            application/json:
              schema:
                $ref: './common-components.yaml#/components/schemas/StandardError'
```

**Best for**: Organizations with multiple APIs sharing common patterns.

### Solution Variant 3: Versioned Component Library (Advanced)
Maintain a governed, versioned library of approved components with:
- Semantic versioning for component packages
- Centralized repository (Git-based or API registry)
- Review and approval processes for changes
- Automated validation ensuring APIs use approved components
- Documentation portal for component discovery

```yaml
# References versioned, centrally-managed components
components:
  schemas:
    User:
      $ref: 'https://api-components.example.com/v2/schemas/User.yaml'
  securitySchemes:
    OAuth2:
      $ref: 'https://api-components.example.com/v1/security/OAuth2.yaml'
```

**Best for**: Large enterprises with mature API governance programs.

### Implementation Practices

**Component Categories to Reuse:**
- **Schemas**: Common domain objects (User, Address, Money, Error)
- **Parameters**: Pagination, filtering, sorting, correlation IDs
- **Responses**: Standard error responses, success patterns
- **Security Schemes**: OAuth2 configurations, API key patterns
- **Headers**: Correlation IDs, rate limiting, API versioning
- **Examples**: Sample requests/responses for testing and documentation

**Governance Model:**
1. **Centralized ownership**: Establish a platform or governance team responsible for the component library
2. **Contribution process**: Define how teams propose new shared components
3. **Review gates**: Require approval before components enter the shared library
4. **Deprecation policy**: Clear process for retiring outdated components
5. **Documentation**: Maintain a catalog explaining when and how to use each component

---

## Contraindications

**Don't use this pattern when:**

- **You have only one or two APIs**: The overhead of maintaining a shared library outweighs benefits for very small API portfolios

- **APIs serve completely different domains**: If your APIs have no overlapping concepts (e.g., a weather API and a payment API), forced reuse creates artificial coupling

- **Extreme performance requirements**: If the overhead of dereferencing external files impacts build or validation performance critically (though this is rare with modern tooling)

- **Team maturity is very low**: Organizations without basic OpenAPI competency should master internal component reuse before attempting cross-API sharing

- **Regulatory isolation required**: When APIs must be completely isolated for compliance reasons (different security classifications, jurisdictions), shared components may violate separation requirements

- **Rapid prototyping phase**: During initial API discovery and design sprints, premature abstraction into shared components can slow exploration; extract components once patterns stabilize. Also, for quick proof-of-concept APIs that are guaranteed not to go into production or integrate with other systems, the overhead of creating and cataloging reusable components might slow down the experimentation phase unnecessarily.

---

## References

**OpenAPI Specification Documentation:**
- [OpenAPI 3.1 Components Object](https://spec.openapis.org/oas/v3.1.0#components-object)
- [OpenAPI Reference Object ($ref)](https://spec.openapis.org/oas/v3.1.0#reference-object)

**Best Practices and Patterns:**
- [API Stylebook - Design Guidelines](http://apistylebook.com/) - Collection of API design guidelines from major organizations
- [Zalando RESTful API Guidelines](https://opensource.zalando.com/restful-api-guidelines/) - Includes component reuse strategies
- [Using Component Libraries for Effective API Governance"](https://blog.stoplight.io/using-component-libraries-for-effective-api-governance) Practical guide to using component libraries on the Stoplight platform. 
- [AEP.dev Common Components](https://aep.dev/213/) - Guidance on common API components on  Application Enhancement Proposals project. 


**Tooling:**
- [Spectral](https://stoplight.io/open-source/spectral) - OpenAPI linting tool that can enforce component reuse rules
- [OpenAPI Generator](https://openapi-generator.tech/) - Supports component references for code generation
- [Redocly](https://redocly.com/) - API registry and governance platform with component management