# Teamcenter PLM & Requirements — Deep Architecture & Integration Guide

> **Scope:** Canonical Single Source of Truth (SSOT), Data Models, Item Revisions, GraphQL & SOA APIs, Requirements Ingestion, and Change Object (`CO`) Workflows for the **FlyNow "Fly in 3"** Digital Thread.

---

## 1. Teamcenter Architecture & Data Model

In the **Siemens AILifecycle Framework**, Teamcenter serves as the immutable data backbone. Autonomous agents (`nx-agent`, `capital-agent`, `simcenter-agent`) do NOT write directly to file systems or local databases; all state modifications are executed via Teamcenter SOA/GraphQL APIs and wrapped inside traceable Teamcenter Change Items.

```
+---------------------------------------------------------------------------------------------------+
|                                TEAMCENTER DOMAIN DATA MODEL TREE                                  |
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
|  [Item: 000452 - FlyNow Flight Vehicle]                                                           |
|    |                                                                                              |
|    +---> Item Revision: 000452/A;1 (Baseline RFP Reqs)                                            |
|            |                                                                                      |
|            +---> RequirementSpec: REQ-SPEC-902 (Polarion Ingested)                                |
|            |       |--> REQ-AERO-001 (Payload Bay Volume >= 0.04 m3)                              |
|            |       |--> REQ-AERO-002 (Max All-Up Weight <= 14.2 kg)                               |
|            |                                                                                      |
|            +---> StructureRevision: EBOM-000452/A (Engineering BOM)                              |
|            |       |--> Design Revision: Spar_Wing_Main/A (NX CAD Body)                           |
|            |       |--> Design Revision: Bulkhead_Forward/A (NX CAD Body)                          |
|            |       |--> Harness Revision: HRN_Avionics_Main/A (Capital EWIS)                       |
|            |                                                                                      |
|            +---> StructureRevision: MBOM-000452/A (Manufacturing BOM)                             |
|            |       |--> Operation 10: Structural Sub-Assembly (LARGE ASSY 2)                      |
|            |       |--> Operation 20: Wiring & Avionics Install (FINAL ASSY 1)                     |
|            |                                                                                      |
|            +---> ChangeItem: CO-2026-0891 (Multi-Domain Change Order)                            |
|                    |--> Affected Items: Spar_Wing_Main/A, HRN_Avionics_Main/A                      |
|                    |--> Verification Status: PASSED (Clash = 0.0mm, SF = 1.62)                     |
|                                                                                                   |
+---------------------------------------------------------------------------------------------------+
```

---

## 2. Requirements Parsing Pipeline (Polarion to Teamcenter)

When a customer Request for Proposal (RFP) is submitted to `https://flynow.xcelerator.us/`, the Teamcenter Agent (`https://tc.xcelerator.us/`) triggers an NLP parser:

### 2.1 JSON Schema for RFP Requirements Ingestion
```json
{
  "project_id": "PRJ-FLYNOW-2026",
  "source_document": "RFP-Aerospace-Drone-V3.pdf",
  "requirement_specification": {
    "name": "FlyNow Vehicle Mission Requirements",
    "requirements": [
      {
        "req_id": "REQ-AERO-001",
        "category": "Spatial",
        "statement": "The payload bay volume shall be greater than or equal to 0.04 cubic meters.",
        "parameter": "payload_volume_m3",
        "target_value": 0.04,
        "operator": ">=",
        "verification_method": "CAD_BOUNDING_BOX_INSPECTION"
      },
      {
        "req_id": "REQ-AERO-002",
        "category": "Mass",
        "statement": "The maximum gross take-off mass shall not exceed 14.2 kilograms.",
        "parameter": "total_mass_kg",
        "target_value": 14.2,
        "operator": "<=",
        "verification_method": "FEA_MASS_PROPERTY_CALCULATION"
      }
    ]
  }
}
```

---

## 3. Teamcenter GraphQL & SOA API Call Patterns

Autonomous agents query and mutate Teamcenter using GraphQL endpoints or SOA REST calls.

### 3.1 Fetching Item Revision & BOM Structure (GraphQL)
```graphql
query GetFlightVehicleBOM($itemId: String!) {
  teamcenterItem(id: $itemId) {
    itemId
    objectName
    latestRevision {
      revisionId
      releaseStatus
      ebomStructure {
        children {
          childItem {
            itemId
            objectName
            objectType
          }
          quantity
          findNumber
        }
      }
    }
  }
}
```

### 3.2 Submitting Change Order (`CO`) for Multi-Agent Edits
```json
POST /tc/api/v1/change/create
{
  "change_type": "EngineeringChangeOrder",
  "name": "CO-2026-0891 - Structural Rib Thickening & Wiring Re-route",
  "synopsis": "Automated update triggered by Simcenter FEA stress breach. Spar web thickened by 1.2mm; Capital harness re-routed.",
  "affected_items": [
    "UID_Spar_Wing_Main_RevA",
    "UID_HRN_Avionics_Main_RevA"
  ],
  "agent_signatures": [
    {"agent": "nx-agent", "action": "GEOMETRY_REBUILD", "status": "SUCCESS"},
    {"agent": "capital-agent", "action": "HARNESS_REROUTE", "status": "SUCCESS"},
    {"agent": "simcenter-agent", "action": "FEA_REVERIFICATION", "status": "PASSED"}
  ]
}
```

---

## 4. Technical Gaps & Roadmap Solutions

1. **Gap:** Traditional Teamcenter SOA relies on poll-based client requests, introducing 500ms–2000ms latency between agent edits.
   - **Solution:** Deploy an SSE / WebSocket broker inside `https://tc.xcelerator.us/` that streams `ItemRevision_Modified` events directly to `AgentCenter`.
2. **Gap:** Simultaneous edits by parallel agents (`nx-agent` and `capital-agent`) can cause write-lock conflicts on Item Revisions.
   - **Solution:** Enforce fine-grained aspect locking (`LockType: ASPECT_GEOMETRY` vs `LockType: ASPECT_WIRING`) within Teamcenter.
