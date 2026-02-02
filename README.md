# Shaping Skills

Claude Code skills for collaborative product shaping and system understanding.

---

## Breadboarding

Breadboarding transforms a workflow description into a complete map of affordances and their relationships. The output is a set of tables showing what you can do and how it wires together — which we can render as a diagram of the shape.

### What Makes Breadboards Different

**Breadboards take the user's point of view.** The "you" in a breadboard is someone trying to *do something* — a user clicking through UI, or a caller invoking an API. Everything in the breadboard is something *you* can act upon, organized by *where you are* (Places).

The question it answers: **"What can I do here, and what happens when I do it?"**

This makes breadboards a **product-level operational view**:
- **What you can do** — the affordances available to you in each place
- **What happens when you do it** — the wiring that connects action to effect
- **Why it doesn't work** — when behavior is wrong, trace the wiring to find what to change

Other diagrams organize around technical concerns (components, types, data structures). Breadboards organize around *capability* — what the system lets you do and how that works underneath.

### Contrast with Other Diagram Types

| Diagram Type | Organizes Around | Breadboard Difference |
|--------------|------------------|----------------------|
| **System Architecture** | Components/services and their boundaries | Breadboards show what you can *do* within components. A "User Service" box becomes N1, N2, N3... specific methods you can call. Architecture says "these services exist." Breadboard says "here's what you can actually do." |
| **Sequence Diagram** | Time — message passing in order | Breadboards show *all possible* wiring, not one scenario. Sequence: "when user clicks Submit, this happens." Breadboard: "here's everything you CAN do and how it connects." |
| **State Chart** | States and transitions | Places resemble states, but breadboards detail what you can do *inside* each place. State chart: "Edit Mode → View Mode on Save." Breadboard: "In Edit Mode, you can U1, U2, U3... which wire to N1, N2..." |
| **Decision Tree** | Branching logic | Breadboards aren't about if/then flow. They're about what you can act on. Conditionals appear as affordances but the organization is "what's available to you," not "what decisions the system makes." |
| **Class Diagram** | Types — properties, methods, inheritance | Breadboards show *behavior you can invoke*, not type definitions. Class diagram: "PostsService has getPostsByFilter()." Breadboard: "You call N4, it wires to N5, returns data to U6." |
| **ER Diagram** | Data entities and their relationships | Breadboards show data stores (S) as *things that enable what you can do* — who writes, who reads, what UI that feeds. ER shows structure; breadboard shows data in service of action. |

### What Breadboards Uniquely Capture

1. **The user's operational reality** — What can I do? What happens when I do it? Why doesn't this work?

2. **UI and Code in one view** — Other diagrams stay in one layer. Breadboards span from button click (U) through handler (N) to store (S) to display (U).

3. **Two explicit flows** — Control flow (Wires Out) and data flow (Returns To) are separated. When something's broken, you can trace which flow is wrong.

4. **Perceptual organization** — Places are about user experience ("can I interact with what's behind?"), not technical boundaries. A modal is a Place because it *blocks you*, not because of how it's implemented.

### When to Use What

| Question | Best Tool |
|----------|-----------|
| How do our services communicate? | System Architecture |
| What happens step-by-step when X occurs? | Sequence Diagram |
| What states can this be in? | State Chart |
| What are the branching conditions? | Decision Tree |
| What types exist and how do they relate? | Class Diagram |
| What's the data model? | ER Diagram |
| **What can users do and how does it work?** | **Breadboard** |

Breadboards fill the gap between "high-level architecture" and "read the code." They show the operational structure at the level of *what you can do* — making them useful for product thinking, debugging behavior, and planning changes.

---

## Shaping

*(Documentation coming soon)*
