## Examples

### 🎯 Start Here: Complete Process Guide

**[Event Modeling Phases Guide](event-modeling-phases-guide.md)** - **RECOMMENDED STARTING POINT**

This comprehensive guide shows:
- The 7 steps of event modeling from brainstorming to implementation
- When to use policies (Step 7, not at the beginning!)
- When to add fields (Step 7, during slicing!)
- Progressive examples of the same system through all phases
- Best practices for workshop facilitation

### Progressive Slicing Examples

See the same subscription billing system evolve through phases:

1. **[Base Model](subscription-billing-base.puml)** - Steps 1-6: Understand the business process (NO policies, NO fields)
2. **[With Policies](subscription-billing-with-policies.puml)** - Step 7a: Identify automation opportunities
3. **[With Business Rules](subscription-billing-with-rules.puml)** - Step 7b: Define policy logic
4. **[Fully Sliced](subscription-billing-sliced.puml)** - Step 7c: Complete with data structures

### Classic Examples

[An iterative example](iterative-example.md) - Shows Steps 1-6 in detail

[An extensive example](extensive-example.md) - Complex multi-aggregate scenario

[Fintech billing system](fintech-billing-system.puml) - Base model with external system integration

### Field Examples

[Simple fields demonstration](simple-fields-demo.puml) - Basic example showing how to add fields to commands, events, views, and policies

[Comprehensive fields example](fields-example.puml) - Demonstrates field usage across all element types including wireframes and personas

---

## Important Principles

1. **Start with the base model** (Steps 1-6) - Don't add policies or fields yet
2. **Policies come during slicing** (Step 7) - After you understand the process
3. **Fields come last** - When you're ready to specify implementation details
4. **Workshop format** - Do this collaboratively with business and technical stakeholders
