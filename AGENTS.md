# AGENTS.md

This repository is designed to work with autonomous and human agents that will receive and process requirements following a strict JSON schema. The schema is stored in `/specifications/specifications.schema.json` and defines the structure and rules for all incoming requirement files.

When an agent is asked to create a spec it is added in the `/specifications/features` folder as a JSON file adhering to the schema specified in `specifications.schema.json`.

## Workflow Overview
1. **Requirement Intake**  
   Agents will receive a JSON document adhering to the schema in `/specifications/`. Each document represents a software change request with metadata, status, and grouped functional and technical requirements.

2. **Status Lifecycle**  
   The `status` property in the schema enforces a predefined lifecycle: `Draft` → `Review` → `Ready-For-Work` → `In-Progress` → `Testing` → `Done`. Special statuses like `Port` are also supported.

3. **Implementation Guidance**  
   - Agents are expected to validate the incoming requirement JSON against the schema before starting work.
   - During `Review`, agents must surface unclear points, request missing details, and link relevant documents to the ticket.
   - In `In-Progress` and `Testing`, agents should maintain fidelity to the original requirements and update linked files accordingly.

4. **Export & Documentation**  
   Completed tickets (`Done`) should have all relevant implementation artifacts linked under `related.documents`. Testing strategies must be documented in the `test-plan` section.

## Schema Highlights
- **id**: UUID for the change request.
- **status**: Enforced workflow stage.
- **type**: Categorizes change (feature, bugfix, performance, refactor, etc.).
- **description / why**: Context and rationale.
- **requirements.functional / requirements.technical**: Separate lists to clarify deliverables.
- **related.documents**: Links or paths to supporting documentation.
- **test-plan**: Describes unit, integration, and manual testing.
- **remarks**: Optional notes for implementers.

Agents and maintainers should treat the schema as the single source of truth for what constitutes a valid requirement in this repository.
