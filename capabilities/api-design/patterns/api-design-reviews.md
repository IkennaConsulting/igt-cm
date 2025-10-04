# API Design Review Pattern


## The Problem
API design decisions have long-lasting consequences that are expensive to change once consumers depend on them. Individual development teams, working in isolation, make design choices that may be locally optimal but create inconsistency across the API portfolio, violate organizational standards, miss security considerations, or introduce usability problems. Without a structured review mechanism, poor API designs reach production, accumulate as technical debt, and degrade the overall developer experience. Organizations need a way to catch design issues early while balancing quality with delivery speed.

---

## Forces
Several competing forces make API design quality difficult to achieve:

- **Speed vs. Quality**: Teams face pressure to deliver APIs quickly, but thorough design takes time and iteration; rushing leads to poor decisions that are costly to fix later

- **Team Autonomy vs. Organizational Consistency**: Development teams want independence to make design decisions, but the organization needs consistency across APIs for a coherent platform experience

- **Expert Knowledge Distribution**: API design expertise is unevenly distributed across teams; some have deep REST/API knowledge while others are learning, yet all need to produce quality APIs

- **Perspective Blindness**: Teams close to their domain develop tunnel vision and miss usability issues obvious to fresh eyes or consumers

- **Cross-Cutting Concerns**: Security, privacy, performance, and compliance requirements span multiple teams, but individual teams may lack specialized knowledge in these areas

- **Async Collaboration Challenges**: Design reviews require coordination across multiple people, potentially in different time zones, making synchronous collaboration difficult

- **Subjective vs. Objective Criteria**: Some API design principles are objective (security flaws, standard violations), while others are subjective (naming preferences, resource modeling), making it hard to achieve consensus

- **Review Bottlenecks**: Centralized review processes can become bottlenecks that slow delivery, but lack of review leads to quality problems

- **Review Timing**: Early reviews on incomplete designs may miss issues, but late reviews after implementation waste work and face resistance to change

---

## Risk the Pattern Mitigates

**API Inconsistency Risk**
Without design reviews, teams make different decisions about authentication, error handling, pagination, naming conventions, and resource modeling, creating a fragmented developer experience across the API portfolio.

**Security Vulnerability Risk**
Design reviews by security-aware reviewers catch authentication flaws, authorization gaps, data exposure issues, and injection vulnerabilities before they reach production, when remediation is exponentially more expensive.

**Poor Usability Risk**
APIs designed without external critique often reflect the provider's mental model rather than consumer needs, resulting in confusing resource structures, unintuitive operations, and poor developer experience that reduces adoption.

**Breaking Change Risk**
Design reviews identify potential evolution challenges early—like missing version strategies, inflexible resource models, or contracts that will be difficult to extend—preventing costly breaking changes later.

**Compliance and Regulatory Risk**
Reviewers with compliance expertise ensure APIs meet GDPR, HIPAA, PCI-DSS, or industry-specific requirements before launch, avoiding regulatory violations and associated penalties.

**Technical Debt Accumulation Risk**
Poor design decisions compound over time as more consumers depend on them. Early design review prevents introducing patterns that teams will regret and struggle to deprecate.

**Knowledge Silos Risk**
Design reviews serve as knowledge-sharing forums where less experienced teams learn from experts, gradually elevating overall API design capability across the organization.

**Documentation and Support Cost Risk**
Poorly designed APIs require extensive documentation to explain confusing patterns and generate higher support costs; design reviews improve intuitive usability, reducing these downstream costs.

**Integration Complexity Risk**
APIs that don't follow predictable patterns increase integration effort for consumers. Reviews ensure consistency that makes each new API easier to adopt based on learned patterns.

---

## How the Solution(s) Work

The API Design Review Pattern establishes a structured process for evaluating API designs before implementation or release, involving stakeholders with diverse perspectives. There are several implementation variants:

### Solution Variant 1: Lightweight Peer Review (Informal)

**Process:**
- API designer shares OpenAPI specification or design document in collaborative tool (GitHub PR, Google Doc, Confluence)
- Designated reviewers (2-3 peers) provide async feedback via comments
- Designer addresses feedback and updates design
- Review considered complete when reviewers approve

**Participants:**
- API designer/owner
- 1-2 peer developers
- Optional: architect or senior engineer

**Artifacts:**
- OpenAPI specification (draft)
- Brief design rationale document
- Review comments and responses

**Advantages:**
- Low overhead and bureaucracy
- Fast turnaround (hours to days)
- Maintains team velocity
- Encourages ownership and collaboration

**Disadvantages:**
- May miss cross-cutting concerns
- Quality depends on reviewer expertise
- Limited organizational consistency

**Best for**: Small organizations, internal APIs, mature teams with strong API design culture, non-critical APIs.

### Solution Variant 2: Design Guild Review (Semi-Formal)

**Process:**
- Voluntary community of practice (API Guild/Chapter) meets regularly
- Teams bring API designs for critique and feedback
- Open discussion format with diverse perspectives
- Designer receives recommendations but retains decision authority
- Guild maintains design guidelines informed by reviews

**Participants:**
- API Guild members (cross-team volunteers)
- API designer presenting
- API architect or platform team members
- Security, compliance, and UX specialists (as needed)

**Meeting Structure:**
- 30-60 minute time-boxed sessions
- Designer presents design (10-15 min)
- Q&A and feedback (30-40 min)
- Summary of key recommendations (5 min)

**Advantages:**
- Balances structure with flexibility
- Knowledge sharing and community building
- Cross-pollination of ideas
- Less formal than approval gates
- Scales through community involvement

**Disadvantages:**
- Recommendations not mandatory (inconsistent enforcement)
- Depends on volunteer engagement
- May lack authority for critical issues
- Can become rubber-stamp if not genuine

**Best for**: Medium-large organizations building API design culture, organizations wanting to avoid heavy governance, teams seeking guidance without hard gates.

### Solution Variant 3: Formal Design Review Board (Structured)

**Process:**
- Mandatory review gate before API implementation or release
- Submission package includes OpenAPI spec, design doc, use cases
- Review board evaluates against organizational standards
- Board issues approval, conditional approval, or rejection
- Tracked through governance workflow system

**Participants:**
- API Review Board (fixed members):
  - API architect (chair)
  - Security representative
  - Compliance/legal representative
  - Senior API developers
  - Product/UX representative
- API designer and team (presenting)

**Review Criteria Checklist:**
- [ ] Consistency with organizational API standards
- [ ] Security and authentication approach
- [ ] Data privacy and compliance
- [ ] Resource modeling and URI design
- [ ] Error handling and observability
- [ ] Versioning and evolution strategy
- [ ] Documentation completeness
- [ ] Performance and scalability considerations
- [ ] Consumer usability and developer experience

**Advantages:**
- Strong quality enforcement
- Comprehensive cross-functional review
- Clear accountability and decision authority
- Catches issues before significant investment
- Effective for high-risk or public APIs

**Disadvantages:**
- Can become bottleneck if not well-managed
- Higher overhead and ceremony
- May slow delivery velocity
- Risk of adversarial relationship between teams and governance
- Requires dedicated governance capacity

**Best for**: Large enterprises, regulated industries, public/partner-facing APIs, organizations with mature governance, high-risk or high-impact APIs.

### Solution Variant 4: Staged Review Process (Hybrid)

**Process:**
- Multiple review stages at different design maturity levels
- **Stage 1 - Concept Review**: Early validation of approach (lightweight, 30 min)
- **Stage 2 - Design Review**: Detailed specification review (formal, 60 min)
- **Stage 3 - Pre-Release Review**: Final check before production (focused, 30 min)
- Later stages can be skipped if no significant changes

**Advantages:**
- Catches issues early when changes are cheap
- Reduces wasted work from late-stage rejections
- Scales review intensity to project maturity
- Balances thoroughness with agility

**Disadvantages:**
- More complex process to manage
- Requires discipline to execute all stages
- Multiple meetings can feel burdensome

**Best for**: Large organizations with complex APIs, projects with high uncertainty, organizations balancing quality and speed.

### Solution Variant 5: Automated Design Review (Tool-Assisted)

**Process:**
- Automated linting tools (Spectral, Optic, API Style Validator) enforce design rules
- Tools check OpenAPI specs against organizational standards
- Failed checks block CI/CD pipeline or require override justification
- Human review focuses on issues tools can't catch

**Automated Checks:**
- Naming conventions (camelCase, snake_case consistency)
- Required security schemes presence
- Standard error response structures
- Pagination parameter consistency
- HTTP method usage (proper verb selection)
- Response code appropriateness
- API versioning presence
- Required metadata fields (descriptions, examples)

**Advantages:**
- Instant feedback during development
- Scales effortlessly across teams
- Consistent, objective enforcement
- Frees human reviewers for higher-value critique
- Reduces review time and bottlenecks

**Disadvantages:**
- Only catches rule-based issues
- Can't evaluate usability or consumer experience
- Over-reliance may miss nuanced problems
- Requires investment in tooling and rule configuration

**Best for**: Organizations of any size as complement to human review, teams with clear API standards, automating objective checks to focus human effort.

---

## Design Review Best Practices

### Pre-Review Preparation

**Designer Responsibilities:**
- Provide OpenAPI specification (complete as possible)
- Document design rationale and alternatives considered
- Identify specific areas where feedback is needed
- Include example use cases or consumer scenarios
- Share at least 48 hours before review meeting

**Reviewer Responsibilities:**
- Review materials in advance
- Prepare specific, constructive feedback
- Focus on principles, not personal preferences
- Come with questions, not just critiques

### During Review

**Facilitation Guidelines:**
- Time-box discussions (use a timer)
- Separate design discussion from implementation concerns
- Encourage psychological safety (no blame)
- Focus on the design, not the designer
- Document decisions and rationale
- Capture action items with owners

**Question Framework (for reviewers):**
- "How will consumers use this? Walk me through the happy path."
- "What happens when...?" (error scenarios)
- "How will this evolve when you need to add...?"
- "Is this consistent with [other similar API]?"
- "What alternatives did you consider and why did you choose this?"

### Review Outputs

**Required Artifacts:**
- Review decision (approved / conditional / rejected)
- Key feedback themes and recommendations
- Action items with owners and deadlines
- Updated OpenAPI specification incorporating feedback
- Design rationale document explaining key decisions

### Review Anti-Patterns to Avoid

**Bike-Shedding**: Spending excessive time on trivial naming debates while missing fundamental design issues. Set time limits and defer subjective minutiae.

**Design by Committee**: Trying to incorporate every suggestion leads to incoherent designs. Designer retains decision authority; reviewers provide input.

**Perfection Paralysis**: Requiring perfect API design before approval prevents shipping. Apply "good enough" threshold for appropriate risk level.

**Late-Stage Surprise**: Catching fundamental issues after implementation. Require concept-level review early in the design process.

**Rubber-Stamping**: Going through motions without genuine critique. Ensure reviewers have time and incentive to provide thoughtful feedback.

**Adversarial Review**: Framing review as gatekeeping rather than collaboration. Foster culture of collective quality ownership.

---

## Contraindications

**Don't use formal design reviews when:**

- **Single developer or very small team**: Overhead exceeds benefit when the entire engineering team is 2-3 people; use external resources or simpler peer review

- **Rapid prototyping or proof-of-concept**: APIs explicitly marked as experimental or temporary don't warrant full review process; defer until productionization

- **Internal microservice communication**: APIs only used by services you control and deploy together don't need external review; coordinate at architecture level instead

- **Incremental changes to existing APIs**: Minor additions or non-breaking changes to established APIs may warrant lighter review; avoid re-reviewing unchanged portions

- **Insufficient reviewer expertise**: If no one in the organization has strong API design knowledge, reviews become blind leading blind; invest in training first or engage external consultants

- **Review becomes pure blocker**: If review process consistently delays projects without providing proportional value, you have process problems—fix the process, don't abandon review

**Adjust review rigor based on:**
- **API audience**: Public > Partner > Internal
- **Change scope**: New API > Major revision > Minor addition
- **Risk level**: Regulated data > Financial > General
- **Reversibility**: Easy to change > Difficult to change

---

## References

**API Design Review Process Documentation:**
- [Google's API Design Guide - Design Reviews](https://google.aip.dev/100) - Includes review process description
- [Microsoft API Review Process](https://github.com/microsoft/api-guidelines/blob/vNext/azure/ConsiderationsForServiceDesign.md) - Azure's approach to API review. Note the Azure responsibilities of the Azure REST API Stewardship board. 


**Books and Comprehensive Guides:**
- "Automating API Delivery: APIOps with OpenAPI" by Ikenna Nwaiwu -  Includes review process considerations
- "The Design of Web APIs" by Arnaud Lauret 



**Articles and Best Practices:**
- [Arnaud Lauret: "How to enhance your API-first design process"](https://blog.postman.com/how-to-enhance-your-api-first-design-process/)
- [Nordic APIs: "A Checklist for Your Next API Design Review"](https://nordicapis.com/a-checklist-for-your-next-api-design-review/)
- James Higginbotham: "API Design Reviews: The Good, The Bad, and The Ugly"

 