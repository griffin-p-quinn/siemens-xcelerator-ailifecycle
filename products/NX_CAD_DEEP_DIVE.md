# NX MCAD & Automation — Deep Architecture & Integration Guide

> **Scope:** Parametric Solid/Surface Modeling, Feature Creation, NX Open API (C#, C++, Python), `griffin-p-quinn/nx-mcp` MCP Server, Synchronous Technology, and Assembly Interference Verification for the **FlyNow "Fly in 3"** Digital Thread.

---

## 1. NX Automation Architecture & Execution Layers

Mechanical design automation in NX operates across three distinct execution tiers:

```
+---------------------------------------------------------------------------------------------------+
|                                   NX CAD AUTOMATION TIER MATRIX                                   |
+---------------------------------------------------------------------------------------------------+
| TIER 1: HIGH-LEVEL PROMPTING (Cloud)  | TIER 2: DISPATCH QUEUE (Fabric) | TIER 3: DEEP MCP EXECUTION (Local)|
| * Endpoint: https://nx.xcelerator.us/ | * Mendix + NX Worker Pool Queue | * Repo: griffin-p-quinn/nx-mcp   |
| * Prompts: "Create 1.2m main spar"    | * Manages NX Headless Licenses  | * Protocol: Model Context Protocol|
| * Output: JSON Feature Request        | * Handles Concurrent CAD Jobs   | * Engine: NX Open C# / Journal    |
+---------------------------------------------------------------------------------------------------+
```

---

## 2. Parametric Expressions & Synchronous Modeling

Autonomous CAD agents interact with NX models through two primary mechanisms:

### 2.1 Driving Parametric Expressions Table
When the agent receives updated requirements from Teamcenter (e.g. `REQ-AERO-002: Max Mass <= 14.2 kg`), it modifies parametric driving expressions:

| Expression Name | Formula / Value | Parameter Description | Triggering Requirement |
| :--- | :--- | :--- | :--- |
| `wing_span_mm` | `1250.0` | Total wingspan tip-to-tip | Payload & Aerodynamic Lift |
| `spar_web_thickness` | `3.2` | Main spar web thickness | Nastran FEA Stress Re-verify |
| `bulkhead_wall_mm` | `2.5` | Avionics mounting wall thickness | Weight reduction target |
| `harness_keepin_r` | `35.0` | Spatial clearance radius for Capital EWIS | Wire bundle diameter |

### 2.2 Synchronous Technology API Automation
When parametric sketch rebuilds fail due to topological changes, the `nx-mcp` server invokes Synchronous Technology APIs (`UF_MODL_move_face`, `UF_MODL_offset_face`) to manipulate 3D boundary faces directly without breaking historical sketch constraints:

```csharp
// C# NX Open Synchronous Face Offset Snippet
using NXOpen;
using NXOpen.Features;

public class NXSynchronousAutomation
{
    public static void OffsetFaceByName(Session session, Part workPart, string faceName, double offsetDistance)
    {
        Face targetFace = workPart.FindObject(faceName) as Face;
        MoveFaceBuilder moveFaceBuilder = workPart.Features.CreateMoveFaceBuilder(null);
        
        moveFaceBuilder.Faces.Add(targetFace);
        moveFaceBuilder.Transform.SetOffsetDistance(offsetDistance);
        
        Feature moveFeature = moveFaceBuilder.Commit();
        moveFaceBuilder.Destroy();
        session.LogFile.WriteLine($"Synchronous offset of {offsetDistance}mm committed on {faceName}");
    }
}
```

---

## 3. Assembly Interference & Clearance Inspection

Before passing geometry from Stage 1 (Design) to Stage 2 (Realize), the NX MCP server runs automated 3D collision inspection across all assembly bodies.

### 3.1 Hard Clash vs. Soft Clearance Inspection Rules
- **Hard Clash:** Intersecting 3D solid volumes ($V_{intersection} > 0.000 \text{ mm}^3$). **Tolerance: 0.000 mm.**
- **Soft Clearance:** Standoff gap between high-voltage Capital wire harnesses and metallic structural ribs. **Tolerance: $\ge 15.0 \text{ mm}$.**
- **Thermal Standoff:** Distance between avionics heat sink fins and composite airframe panels. **Tolerance: $\ge 25.0 \text{ mm}$.**

---

## 4. Technical Gaps & Roadmap Solutions

1. **Gap:** Running multiple concurrent headless NX sessions consumes expensive floating license tokens.
   - **Solution:** Utilize Griffin's Mendix Digital Fabric worker queue to pool and reuse active NX sessions without terminating/restarting executable instances.
2. **Gap:** Complex parametric sketches can experience topological flips when dimensions vary by > 50%.
   - **Solution:** Combine sketch-based parametric modeling with Synchronous Technology face moves when parameter deltas exceed 30%.
