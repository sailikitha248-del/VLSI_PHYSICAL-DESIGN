# RTL-to-GDSII: Routing and Design Rule Verification

## 1. Routing

Routing is the physical-design stage in which the electrical connections
between placed standard cells, macros, pins, and other design elements
are implemented using the available metal layers and vias.

The routing stage generally consists of:

-   Global or fast routing
-   Detailed routing
-   Design Rule Checking (DRC)
-   Connectivity verification
-   Optimization of wire length and via usage
   <img width="737" height="580" alt="Screenshot 2026-09-26 205703" src="https://github.com/user-attachments/assets/4cb0f725-e271-4637-9534-81315786b6fc" />

<img width="1240" height="572" alt="image" src="https://github.com/user-attachments/assets/019ca4d4-78f0-49f2-98a5-6445145e4169" />

------------------------------------------------------------------------

## 2. Maze Routing -- Lee's Algorithm

Maze routing is a grid-based routing technique used to find a path
between a source and a target while avoiding obstacles.

### Basic Procedure

1.  Represent the routing region as a grid.
2.  Assign the source point an initial value.
3.  Propagate values to neighboring grid points.
4.  Continue the propagation until the target is reached.
5.  Trace the path backward from the target to the source.
6.  The resulting path is used as the routed connection.

The propagation continues through available grid locations until a
target location is reached.

------------------------------------------------------------------------

## 3. Design Rule Checking (DRC)

**DRC (Design Rule Checking)** verifies whether the physical layout
satisfies the manufacturing and technology design rules.

When two wires are created, the geometry must satisfy the minimum
required separation between them.

### Typical Metal Design Rules

The important design rules for metal interconnects include:

1.  **Wire Width**
2.  **Wire Pitch**
3.  **Wire Spacing**

### Via Design Rules

Important via-related rules include:

1.  **Via Width**
2.  **Via Spacing**

These rules help ensure that the routed layout can be manufactured
correctly and that unwanted shorts or other physical violations are
avoided.

------------------------------------------------------------------------
<img width="1306" height="580" alt="image" src="https://github.com/user-attachments/assets/53cbca78-d3cb-46ba-8e88-7219253373ee" />

# 4. Routing Using TritonRoute

Routing can be divided into two major stages:

``` text
                 +----------------+
                 |    ROUTING     |
                 +-------+--------+
                         |
                 +-------+-------+
                 |               |
                 v               v
          +-------------+  +-------------+
          | FAST ROUTE  |  | DETAIL ROUTE|
          +-------------+  +-------------+
```

## 4.1 TritonRoute

TritonRoute is a detailed-routing engine used in the OpenROAD
physical-design flow.

Its routing process includes the following concepts:

-   Performs detailed routing after the initial routing stage.
-   Uses the preprocessed route guides generated during fast/global
    routing.
-   Attempts to follow the available route guides as closely as
    possible.
-   Maintains required connectivity for every net.
-   Uses a panel-based routing approach.
-   Supports intra-layer parallel routing and inter-layer sequential
    routing.

------------------------------------------------------------------------

# 5. Route Guides

Route guides provide preferred routing regions for nets and help the
detailed router determine where connections should be implemented.

### Requirements of Route Guides

A route guide should:

-   Have an appropriate width.
-   Follow a preferred routing direction.
-   Provide sufficient coverage for the required connection.

------------------------------------------------------------------------

# 6. Inter-Guide Connectivity

Two route guides can be considered connected when their geometry
provides a valid electrical connection.

Typical cases include:

### Same Metal Layer

Two guides can connect when they are on the same metal layer and their
edges touch or overlap appropriately.

### Neighboring Metal Layers

Guides on neighboring metal layers can be connected when they have a
non-zero vertical overlap area, allowing a via connection.

### Terminal Connectivity

An unconnected terminal, such as a standard-cell pin, must have its pin
shape appropriately overlapped by a route guide so that the detailed
router can establish the connection.

------------------------------------------------------------------------
<img width="792" height="506" alt="Screenshot 2026-09-26 203719" src="https://github.com/user-attachments/assets/66fa7985-e399-49b0-a3fa-a08cc4ffcecd" />

# 7. Reprocessed Route Guides

Reprocessed route guides are route guides prepared for the
detailed-routing stage.

They should satisfy the following requirements:

-   Appropriate routing width.
-   Preferred routing direction.
-   Valid connectivity between adjacent guides.
-   Sufficient overlap for layer transitions.
-   Proper connection to standard-cell pins and other terminals.

------------------------------------------------------------------------

# 8. Routing Topology

A routing topology represents the connectivity structure that determines
how a net can be routed between its terminals.

The routing topology is used to organize connections before detailed
routing and to provide valid paths between different terminals of a net.

Important concepts include:

-   Routing grid
-   Metal-layer connectivity
-   Access points
-   Layer transitions
-   Route-guide connectivity
-   Routing paths

------------------------------------------------------------------------

# 9. Access Points

An **Access Point (AP)** is a location on the routing grid used to
connect a route guide to another routing element.

An access point can be used to connect:

-   A lower metal-layer segment to an upper metal-layer segment.
-   A route segment to a pin.
-   A route guide to an upper-layer routing segment.
-   A routing segment through a via connection.

## 9.1 Access Point Cluster

An **Access Point Cluster (APC)** is a collection of access points
derived from the same connection source, such as:

-   A lower-layer segment
-   An upper-layer route guide
-   A pin
-   An I/O port

Access-point selection is important for maintaining connectivity while
satisfying routing and design-rule constraints.

------------------------------------------------------------------------

# 10. Panel-Based Routing

Panel-based routing divides the routing region into panels and processes
them systematically.

The routing process can use:

-   Intra-layer parallel routing
-   Inter-layer sequential routing

This approach allows routing work to be organized by metal layers and
panels while maintaining the required connectivity between nets.

### Overall Routing Flow

A typical panel-routing sequence can be represented as:

``` text
Panel routing on M2
        |
        v
Parallel routing of selected panels
        |
        v
Routing on the next metal layer
        |
        v
Sequential processing of subsequent panels
```

The process continues across the required metal layers until the routing
requirements are satisfied.

------------------------------------------------------------------------

# 11. Access-Point Connectivity

Access points can be used for several types of connections:

### A. Connection to a Lower-Layer Segment

An access point can connect a routing segment on an upper metal layer to
a compatible segment on a lower metal layer.

### B. Connection to a Pin

An access point can establish connectivity between a routing path and
the physical pin shape of a standard cell.

### C. Connection to an Upper Metal Layer

An access point can connect a lower-layer routing structure to a higher
metal layer, typically through an appropriate via.

These connections enable the detailed router to construct complete paths
between terminals.

------------------------------------------------------------------------

# 12. Routing Constraints

The detailed routing solution must satisfy several constraints
simultaneously.

### Route-Guide Constraints

Routing should remain within or as close as possible to the specified
route-guide regions.

### Connectivity Constraints

Every terminal belonging to the same net must be electrically connected.

### Design-Rule Constraints

The routed geometry must satisfy the required technology rules,
including:

-   Minimum wire width
-   Minimum spacing
-   Via dimensions
-   Via spacing
-   Metal-layer restrictions
-   Preferred routing directions

### Optimization Objectives

Routing can also consider:

-   Wire length
-   Via count
-   Routing congestion
-   Number of design-rule violations
-   Connectivity
-   Available routing resources

------------------------------------------------------------------------

# 13. Routing Problem Statement

## Inputs

The detailed-routing stage can use:

-   LEF
-   DEF
-   Reprocessed route guides

## Output

A detailed routing solution with optimized:

-   Wire length
-   Via count

## Constraints

The routing solution must satisfy:

-   Route-guide constraints
-   Connectivity constraints
-   Design-rule constraints
-   Metal-layer and routing-direction constraints

------------------------------------------------------------------------

# 14. Important Routing Concepts

  -----------------------------------------------------------------------
  Concept                             Description
  ----------------------------------- -----------------------------------
  **Maze Routing**                    Grid-based method for finding paths
                                      between source and target locations

  **Lee's Algorithm**                 Wave-propagation-based maze-routing
                                      algorithm

  **Fast Route**                      Initial/global routing stage that
                                      produces routing guidance

  **Detailed Route**                  Final routing stage that creates
                                      exact physical interconnects

  **Route Guide**                     Preferred routing region used by
                                      detailed routing

  **Access Point**                    Location used to establish
                                      connectivity between routing
                                      structures

  **APC**                             Group of access points associated
                                      with a common connection source

  **Panel Routing**                   Routing approach that processes the
                                      routing region panel by panel

  **DRC**                             Verification of layout against
                                      physical design rules

  **Wire Width**                      Physical width of a metal
                                      interconnect

  **Wire Spacing**                    Required separation between metal
                                      geometries

  **Wire Pitch**                      Repeated center-to-center spacing
                                      of routing tracks

  **Via Width**                       Physical dimension of a via

  **Via Spacing**                     Required separation between vias
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 15. OpenLane Flow Commands

The routing-related physical-design flow can be initiated after
synthesis and floorplanning.

### Enter the OpenLane Flow

``` bash
./flow.tcl -interactive
```

### Prepare the Design

``` tcl
prep -design picorv32a -overwrite
```

### Run Synthesis

``` tcl
run_synthesis
```

### Generate Power Distribution Network

``` tcl
gen_pdn
```

After floorplanning, placement, clock-tree synthesis, and routing
stages, the generated DEF and other physical-design files can be
inspected for connectivity and design-rule correctness.

------------------------------------------------------------------------

# 16. Typical RTL-to-GDSII Routing Sequence

``` text
RTL
 |
 v
Synthesis
 |
 v
Gate-Level Netlist
 |
 v
Floorplanning
 |
 v
Power Distribution Network
 |
 v
Placement
 |
 v
Clock Tree Synthesis
 |
 v
Global / Fast Routing
 |
 v
Detailed Routing
 |
 v
DRC
 |
 v
LVS / Connectivity Verification
 |
 v
GDSII
```

------------------------------------------------------------------------

# 17. Key Takeaways

-   Routing creates physical interconnections between circuit elements.
-   Maze routing treats the routing area as a grid and searches for a
    valid path.
-   Lee's algorithm uses wave propagation to find a route between
    terminals.
-   Fast/global routing provides routing guidance for detailed routing.
-   Detailed routing converts routing guidance into physical metal and
    via geometries.
-   TritonRoute performs detailed routing while considering route guides
    and connectivity.
-   Access points are essential for connecting pins and routing segments
    across metal layers.
-   Panel-based routing organizes routing operations across portions of
    the routing grid.
-   DRC verifies that the physical layout follows technology-specific
    manufacturing rules.
-   Wire width, wire pitch, wire spacing, via width, and via spacing are
    important physical constraints.
-   A valid final routing solution must satisfy connectivity,
    route-guide, and design-rule constraints.
