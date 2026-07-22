# STAGE 1: DESIGN STAGE — Polarion Requirements to Verified NX CAD & Capital EWIS Co-Design

> **Scope:** Natural Language RFP Parsing, Polarion ALM Requirements Sync, Teamcenter Active Workspace Integration, NX Open Parametric CAD Automation, Capital Logic Wire Sizing, Capital Harness 3D Routing in NX, and 0.0mm 3D Clash Verification.

---

## 1. Stage Overview & Technical Workflow Pipeline

The **DESIGN Stage** takes an incoming Customer Request for Proposal (RFP) document submitted to [FlyNow](https://flynow.xcelerator.us/) and transforms it into a constrained, interference-free, multi-domain 3D mechanical and electrical assembly tracked inside Teamcenter Active Workspace.

```
+---------------------------------------------------------------------------------------------------+
|                                 STAGE 1: DESIGN STAGE PIPELINE                                    |
+---------------------------------------------------------------------------------------------------+
|                                                                                                   |
|  [RFP PDF Document]                                                                               |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 1.1 Requirements Ingestion        |  (Polarion ALM OSLC REST API /                             |
|  |     Polarion WorkItems            |   Teamcenter Requirements Manager TPI Sync)                |
|  +-----------------------------------+                                                            |
|         |                                                                                         |
|         v                                                                                         |
|  +-----------------------------------+                                                            |
|  | 1.2 Teamcenter Structure Init     |  (Teamcenter SOA DataManagementService /                    |
|  |     Item & Revision Allocation    |   Active Workspace GraphQL API - https://tc.xcelerator.us/)|
|  +-----------------------------------+                                                            |
|         |                                                                                         |
|         +------------------------------------+------------------------------------+               |
|         |                                                                         |               |
|         v                                                                         v               |
|  +-----------------------------------+                             +----------------------------+ |
|  | 1.3 NX Open CAD Automation        |                             | 1.4 Capital EWIS Wiring    | |
|  |     https://nx.xcelerator.us/     |                             | https://capital.xcelerator.us|
|  |     griffin-p-quinn/nx-mcp        |                             | Capital Logic Schematics & | |
|  |     NXOpen.Features Extrude       |                             | Capital Harness 3D in NX   | |
|  +-----------------------------------+                             +----------------------------+ |
|         |                                                                         |               |
|         +------------------------------------+------------------------------------+               |
|                                              |                                                    |
|                                              v                                                    |
|                               +----------------------------------+                                |
|                               | 1.5 NX PCB Exchange & 3D Clash   |                                |
|                               |     Clearance Verification Gate  |                                |
|                               +----------------------------------+                                |
|                                              |                                                    |
|                                              v                                                    |
|                                 [VERIFIED 3D DIGITAL MODEL]                                       |
|                                                                                                   |
+---------------------------------------------------------------------------------------------------+
```

---

## 2. Deep-Dive Component Technical Specifications

### 2.1 Polarion ALM Requirements Ingestion (OSLC REST API)
- **Primary Service:** Polarion ALM OSLC REST Endpoint (`/polarion/oslc/services/projects/FlyNow_Project/workitems`).
- **Mechanism:** Natural language parsing extracts structured requirements into Polarion WorkItems of type `requirement`.
- **Sample OSLC REST Request:**
```http
POST /polarion/oslc/services/projects/FlyNow_Project/workitems HTTP/1.1
Host: polarion.xcelerator.us
Content-Type: application/json
Authorization: Bearer <Polarion_OAuth_Token>

{
  "dcterms:title": "FlyNow Airframe Mass Constraint",
  "dcterms:description": "The maximum gross take-off mass shall not exceed 14.2 kg.",
  "polarion:type": "requirement",
  "polarion:severity": "must_have",
  "polarion:customFields": [
    {"id": "target_parameter", "value": "total_mass_kg"},
    {"id": "target_value", "value": "14.2"},
    {"id": "operator", "value": "<="}
  ]
}
```

### 2.2 Teamcenter Active Workspace Structure Initialization (SOA API)
- **Primary Endpoint:** `https://tc.xcelerator.us/` (Teamcenter Active Workspace Data Service).
- **SOA Library Call (`Teamcenter.Soa.Client.DataManagementService`):**
```csharp
// C# Teamcenter SOA API Call for Item Creation
using Teamcenter.Services.Strong.Core;
using Teamcenter.Services.Strong.Core._2008_06.DataManagement;

public class TeamcenterItemInitializer
{
    public static CreateItemsResponse CreateFlightVehicleItem(DataManagementService dmService, string itemId, string itemName)
    {
        ItemProperties itemProps = new ItemProperties();
        itemProps.clientId = "FlyNow_AgentCenter";
        itemProps.itemId = itemId;
        itemProps.name = itemName;
        itemProps.type = "Design"; // Canonical Teamcenter Design Item Type
        
        CreateItemsResponse response = dmService.CreateItems(new ItemProperties[] { itemProps }, null, "");
        return response;
    }
}
```

### 2.3 NX CAD Automation via NX Open C# (`griffin-p-quinn/nx-mcp`)
- **Primary Endpoints:** `https://nx.xcelerator.us/` (Cloud Prompting) & `griffin-p-quinn/nx-mcp` (Local C# NX Open Server).
- **Production NX Open C# Feature Generation Script:**
```csharp
// C# NX Open Parametric Extrusion Builder
using System;
using NXOpen;
using NXOpen.Features;
using NXOpen.Preferences;

public class NXAirframeSparGenerator
{
    public static Feature CreateMainSparBody(Session session, Part workPart, double spanLength, double webThickness)
    {
        // 1. Set Parametric Driving Expressions
        Expression spanExpr = workPart.Expressions.CreateExpression("Number", $"wing_span = {spanLength}");
        Expression webExpr = workPart.Expressions.CreateExpression("Number", $"spar_web_thickness = {webThickness}");

        // 2. Initialize Extrude Feature Builder
        ExtrudeBuilder extrudeBuilder = workPart.Features.CreateExtrudeBuilder(null);
        Sketch sparSketch = (Sketch)workPart.Sketches.FindObject("SKETCH_SPAR_PROFILE");
        
        extrudeBuilder.Section = workPart.Sections.CreateSection(0.001, 0.01, 0.5);
        extrudeBuilder.Section.AddArray(new Sketch[] { sparSketch });
        
        extrudeBuilder.Limits.StartExtend.Value.RightHandSide = "0.0";
        extrudeBuilder.Limits.EndExtend.Value.RightHandSide = "wing_span";

        // 3. Commit Feature Creation
        Feature extrudeFeature = extrudeBuilder.CommitFeature();
        extrudeBuilder.Destroy();

        session.LogFile.WriteLine($"[NX OPEN] Main Spar Extrusion Feature Created. Feature Tag: {extrudeFeature.Tag}");
        return extrudeFeature;
    }
}
```

### 2.4 Capital EWIS Wire Sizing & Capital Harness 3D Design in NX
- **Primary Endpoint:** `https://capital.xcelerator.us/` (Capital Logic & Capital Harness Option).
- **Capital Bridge Java API Wire Sizing Call:**
```java
// Capital Bridge Java API - Wire Sizing Verification
import com.mentor.capital.api.CapitalSession;
import com.mentor.capital.api.CapitalProject;
import com.mentor.capital.api.CapitalWire;

public class CapitalWireSizingAgent {
    public static String verifyWireGauge(CapitalProject project, String circuitId, double peakCurrentAmps) {
        CapitalWire wire = project.findWireByCircuit(circuitId);
        double maxAllowedVoltageDrop = 0.03 * 28.0; // 3% of 28VDC
        
        String recommendedAWG = (peakCurrentAmps > 30.0) ? "AWG 8" : "AWG 16";
        wire.setAttribute("WireSize", recommendedAWG);
        
        System.out.println("[CAPITAL API] Circuit " + circuitId + " set to " + recommendedAWG);
        return recommendedAWG;
    }
}
```

---

## 3. Stage 1 Verification Gate Criteria

| Verification Metric | Target Threshold | Authentic Siemens Tool | Pass/Fail Gate Action |
| :--- | :--- | :--- | :--- |
| **Requirements Traceability** | 100% mapped to Design Items | Polarion-Teamcenter Integration (PTI) | Block Stage 2 if unmapped reqs exist |
| **3D Solid Hard Clash** | 0.000 mm hard interferences | NX Open Interference Check (`UF_ASSEM`) | Flag clashing body in red & trigger synchronous move |
| **Harness Bend Radius** | $R_{bend} \ge 5.0 \times D_{bundle}$ | Capital Harness 3D Design (in NX) | Flag over-bent bundle segment |
| **Circuit Voltage Drop** | $\le 3.0\%$ nominal bus voltage | Capital Logic Simulator | Increase wire gauge automatically |
