
## Automaton5BL_Project_local.ipynb

ChatGPT generated:

This repository contains code, notes, and tooling for designing and
analyzing a **5-Bar Linkage (5BL)** based automaton.\
The project covers the full pipeline:

1.  Task-space trajectory planning\
2.  Inverse and forward kinematics of a 5-bar linkage\
3.  Conversion of joint motion to cam--follower motion\
4.  Generation and export of cam profile geometry

The repository is intended for collaborators who will extend or maintain
the codebase.\
This document explains the structure, conventions, and workflow.

------------------------------------------------------------------------

## Project Overview

### 1. FiveBL_Kinema --- 5-Bar Linkage Kinematics

The class `FiveBL_Kinema` provides kinematic utilities for a symmetric
5-bar linkage.

Included functionality: - Forward kinematics:\
Computes `(x, y)` of the end-effector given joint angles `(q1, q2)`. -
Inverse kinematics:\
Computes `(q1, q2)` for a desired end-effector position `(x, y)`. -
Workspace consistency tests:\
The notebook contains scripts that sample many points, apply IK then FK,
and verify reconstruction error.

This module is the foundation for converting desired task-space motion
into joint trajectories.

------------------------------------------------------------------------

### 2. Cam_Kinema --- Follower--Cam Geometry

A second class handles the geometry of a rotating cam interacting with
oscillating followers.

Capabilities: - Given follower angular displacement `ψ`, compute
follower tip coordinates. - Apply rotation by cam angle `φ` to map
follower motion to global coordinates. - Evaluate follower paths
corresponding to joint motion of the 5BL. - Generate full cam profiles
for both left and right followers.

This forms the bridge between joint-space motion and physical cam
geometry.

------------------------------------------------------------------------

### 3. Integration: From Task Trajectory → Joint Space → Cam Profiles

The notebook provides a pipeline:

1.  Start with a parameterized trajectory `P(φ) = (x(φ), y(φ))` in task
    space.\
2.  Convert to joint-space paths `(q1(φ), q2(φ))` using 5BL inverse
    kinematics.\
3.  Convert joint angles to follower positions.\
4.  From follower displacements, generate cam profiles suitable for
    fabrication.

------------------------------------------------------------------------

### 4. Validation and Visualization

The notebook includes: - Scatter comparisons of original vs
reconstructed trajectories\
- Visual confirmation of follower paths\
- Test runs of full pipelines with sample trajectories\
- Plots of cam profiles in global coordinates

All tests are meant to help collaborators verify correctness when
modifying equations or geometry.

------------------------------------------------------------------------

### 5. Exporting Geometry

The final section exports cam profile points for downstream use (CAD,
CNC, or simulation).\
Exports include: - Left cam profile\
- Right cam profile\
- Synchronized sampling across cam rotation\
- Output formats suitable for DXF/SVG conversion scripts (not part of
this repo)

------------------------------------------------------------------------

## File Structure

    Automaton5BL_Project_local.ipynb   # Main project notebook

The notebook contains the complete workflow as well as the two main
classes:

-   `FiveBL_Kinema` --- inverse/forward kinematics for the 5-bar
    linkage\
-   `Cam_Kinema` --- follower geometry and cam profile generation

Below are the key parameters and main methods that collaborators will
need.

------------------------------------------------------------------------

## Important Mechanism Dimensions

### FiveBL_Kinema

These define the geometry of the 5-bar linkage:

``` python
self.d   # base separation between the left and right ground joints
self.l1  # left input link length
self.l2  # right input link length
self.l3  # left floating link length
self.l4  # right floating link length
```

Default values:

-   `d = 0.0` cm\
-   `l1 = l2 = l3 = l4 = 15.0` cm

The mechanism is currently symmetric. If dimensions change, re-validate
IK/FK accuracy.

### Cam_Kinema

Defines the follower mechanism and cam placement:

``` python
self.l_follower     # follower arm length
self.ang_offset     # angular offset at zero displacement
self.a              # distance from follower base to cam center
```

Default values:

-   `l_follower = 10.0` cm\
-   `ang_offset = 20°`\
-   `a = 10.5` cm

These directly affect cam geometry; modify carefully.
