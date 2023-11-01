
<a id='API-Reference'></a>

<a id='API-Reference-1'></a>

# API Reference


*This is the private internal documentation of the Ribasim API.*

- [API Reference](index.md#API-Reference)
    - [Modules](index.md#Modules)
    - [Types](index.md#Types)
    - [Functions](index.md#Functions)
    - [Constants](index.md#Constants)
    - [Macros](index.md#Macros)
    - [Index](index.md#Index)


<a id='Modules'></a>

<a id='Modules-1'></a>

## Modules

<a id='Ribasim.Ribasim' href='#Ribasim.Ribasim'>#</a>
**`Ribasim.Ribasim`** &mdash; *Module*.



```julia
module Ribasim
```

Ribasim is a water resources model. The computational core is implemented in Julia in the Ribasim package. It is currently mainly designed to be used as an application. To run a simulation from Julia, use [`Ribasim.run`](index.md#Ribasim.run-Tuple{AbstractString}).

For more granular access, see:

  * [`Config`](index.md#Ribasim.config.Config-Tuple{AbstractString})
  * [`Model`](index.md#Ribasim.Model)
  * [`solve!`](index.md#CommonSolve.solve!-Tuple{Ribasim.Model})
  * [`BMI.finalize`](index.md#BasicModelInterface.finalize-Tuple{Ribasim.Model})


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/Ribasim.jl#LL1-L14' class='documenter-source'>source</a><br>

<a id='Ribasim.config' href='#Ribasim.config'>#</a>
**`Ribasim.config`** &mdash; *Module*.



```julia
module config
```

Ribasim.config is a submodule of [`Ribasim`](index.md#Ribasim.Ribasim) to handle the configuration of a Ribasim model. It is implemented using the [Configurations](https://configurations.rogerluo.dev/stable/) package. A full configuration is represented by [`Config`](index.md#Ribasim.config.Config-Tuple{AbstractString}), which is the main API. Ribasim.config is a submodule mainly to avoid name clashes between the configuration sections and the rest of Ribasim.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/config.jl#LL1-L8' class='documenter-source'>source</a><br>


<a id='Types'></a>

<a id='Types-1'></a>

## Types

<a id='Ribasim.AllocationModel' href='#Ribasim.AllocationModel'>#</a>
**`Ribasim.AllocationModel`** &mdash; *Type*.



Store information for a subnetwork used for allocation.

node*id: All the IDs of the nodes that are in this subnetwork node*id*mapping: Mapping Dictionary; model*node*id => AG*node*id where such a correspondence exists     (all AG node ids are in the values) node*id*mapping*inverse: The inverse of node*id*mapping, Dictionary; AG node ID => model node ID Source edge mapping: AG source node ID => subnetwork source edge ID graph*allocation: The graph used for the allocation problems capacity: The capacity per edge of the allocation graph, as constrained by nodes that have a max*flow*rate problem: The JuMP.jl model for solving the allocation problem Δt*allocation: The time interval between consecutive allocation solves


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL7-L19' class='documenter-source'>source</a><br>

<a id='Ribasim.AllocationModel-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}, Float64}' href='#Ribasim.AllocationModel-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}, Float64}'>#</a>
**`Ribasim.AllocationModel`** &mdash; *Method*.



Construct the JuMP.jl problem for allocation.

**Definitions**

  * 'subnetwork' is used to refer to the original Ribasim subnetwork;
  * 'allocgraph' is used to refer to the allocation graph.

**Inputs**

p: Ribasim problem parameters subnetwork*node*ids: the problem node IDs that are part of the allocation subnetwork source*edge*ids:: The IDs of the edges in the subnetwork whose flow fill be taken as     a source in allocation Δt_allocation: The timestep between successive allocation solves

**Outputs**

An AllocationModel object.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL693-L713' class='documenter-source'>source</a><br>

<a id='Ribasim.Basin' href='#Ribasim.Basin'>#</a>
**`Ribasim.Basin`** &mdash; *Type*.



Requirements:

  * Must be positive: precipitation, evaporation, infiltration, drainage
  * Index points to a Basin
  * volume, area, level must all be positive and monotonic increasing.

Type parameter C indicates the content backing the StructVector, which can be a NamedTuple of vectors or Arrow Tables, and is added to avoid type instabilities. The node*id are Indices to support fast lookup of e.g. current*level using ID.

if autodiff     T = DiffCache{Vector{Float64}} else     T = Vector{Float64} end


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL97-L113' class='documenter-source'>source</a><br>

<a id='Ribasim.Connectivity' href='#Ribasim.Connectivity'>#</a>
**`Ribasim.Connectivity`** &mdash; *Type*.



Store the connectivity information

graph*flow, graph*control: directed graph with vertices equal to ids flow: store the flow on every flow edge edge*ids*flow, edge*ids*control: get the external edge id from (src, dst) edge*connection*type*flow, edge*connection*types*control: get (src*node*type, dst*node*type) from edge id

if autodiff     T = DiffCache{SparseArrays.SparseMatrixCSC{Float64, Int64}, Vector{Float64}} else     T = SparseMatrixCSC{Float64, Int} end


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL31-L44' class='documenter-source'>source</a><br>

<a id='Ribasim.DiscreteControl' href='#Ribasim.DiscreteControl'>#</a>
**`Ribasim.DiscreteControl`** &mdash; *Type*.



node*id: node ID of the DiscreteControl node; these are not unique but repeated     by the amount of conditions of this DiscreteControl node listen*feature*id: the ID of the node/edge being condition on variable: the name of the variable in the condition greater*than: The threshold value in the condition condition*value: The current value of each condition control*state: Dictionary: node ID => (control state, control state start) logic_mapping: Dictionary: (control node ID, truth state) => control state record: Namedtuple with discrete control information for results


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL386-L396' class='documenter-source'>source</a><br>

<a id='Ribasim.FlatVector' href='#Ribasim.FlatVector'>#</a>
**`Ribasim.FlatVector`** &mdash; *Type*.



```julia
struct FlatVector{T} <: AbstractVector{T}
```

A FlatVector is an AbstractVector that iterates the T of a `Vector{Vector{T}}`.

Each inner vector is assumed to be of equal length.

It is similar to `Iterators.flatten`, though that doesn't work with the `Tables.Column` interface, which needs `length` and `getindex` support.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL876-L885' class='documenter-source'>source</a><br>

<a id='Ribasim.FlowBoundary' href='#Ribasim.FlowBoundary'>#</a>
**`Ribasim.FlowBoundary`** &mdash; *Type*.



node*id: node ID of the FlowBoundary node active: whether this node is active and thus contributes flow flow*rate: target flow rate


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL279-L283' class='documenter-source'>source</a><br>

<a id='Ribasim.FractionalFlow' href='#Ribasim.FractionalFlow'>#</a>
**`Ribasim.FractionalFlow`** &mdash; *Type*.



Requirements:

  * from: must be (TabulatedRatingCurve,) node
  * to: must be (Basin,) node
  * fraction must be positive.

node*id: node ID of the TabulatedRatingCurve node fraction: The fraction in [0,1] of flow the node lets through control*mapping: dictionary from (node*id, control*state) to fraction


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL251-L261' class='documenter-source'>source</a><br>

<a id='Ribasim.LevelBoundary' href='#Ribasim.LevelBoundary'>#</a>
**`Ribasim.LevelBoundary`** &mdash; *Type*.



node_id: node ID of the LevelBoundary node active: whether this node is active level: the fixed level of this 'infinitely big basin'


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL268-L272' class='documenter-source'>source</a><br>

<a id='Ribasim.LinearResistance' href='#Ribasim.LinearResistance'>#</a>
**`Ribasim.LinearResistance`** &mdash; *Type*.



Requirements:

  * from: must be (Basin,) node
  * to: must be (Basin,) node

node*id: node ID of the LinearResistance node active: whether this node is active and thus contributes flows resistance: the resistance to flow; Q = Δh/resistance control*mapping: dictionary from (node*id, control*state) to resistance and/or active state


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL190-L200' class='documenter-source'>source</a><br>

<a id='Ribasim.ManningResistance' href='#Ribasim.ManningResistance'>#</a>
**`Ribasim.ManningResistance`** &mdash; *Type*.



This is a simple Manning-Gauckler reach connection.

  * Length describes the reach length.
  * roughness describes Manning's n in (SI units).

The profile is described by a trapezoid:

```
     \            /  ^
      \          /   |
       \        /    | dz
bottom  \______/     |
^               <--->
|                 dy
|        <------>
|          width
|
|
+ datum (e.g. MSL)
```

With `profile_slope = dy / dz`. A rectangular profile requires a slope of 0.0.

Requirements:

  * from: must be (Basin,) node
  * to: must be (Basin,) node
  * length > 0
  * roughess > 0
  * profile_width >= 0
  * profile_slope >= 0
  * (profile*width == 0) xor (profile*slope == 0)


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL208-L240' class='documenter-source'>source</a><br>

<a id='Ribasim.Model' href='#Ribasim.Model'>#</a>
**`Ribasim.Model`** &mdash; *Type*.



```julia
Model(config_path::AbstractString)
Model(config::Config)
```

Initialize a Model.

The Model struct is an initialized model, combined with the [`Config`](index.md#Ribasim.config.Config-Tuple{AbstractString}) used to create it and saved results. The Basic Model Interface ([BMI](https://github.com/Deltares/BasicModelInterface.jl)) is implemented on the Model. A Model can be created from the path to a TOML configuration file, or a Config object.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/lib.jl#LL1-L10' class='documenter-source'>source</a><br>

<a id='Ribasim.Outlet' href='#Ribasim.Outlet'>#</a>
**`Ribasim.Outlet`** &mdash; *Type*.



node*id: node ID of the Outlet node active: whether this node is active and thus contributes flow flow*rate: target flow rate min*flow*rate: The minimal flow rate of the outlet max*flow*rate: The maximum flow rate of the outlet control*mapping: dictionary from (node*id, control*state) to target flow rate is*pid_controlled: whether the flow rate of this outlet is governed by PID control


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL333-L341' class='documenter-source'>source</a><br>

<a id='Ribasim.PidControl' href='#Ribasim.PidControl'>#</a>
**`Ribasim.PidControl`** &mdash; *Type*.



PID control currently only supports regulating basin levels.

node*id: node ID of the PidControl node active: whether this node is active and thus sets flow rates listen*node*id: the id of the basin being controlled pid*params: a vector interpolation for parameters changing over time.     The parameters are respectively target, proportional, integral, derivative,     where the last three are the coefficients for the PID equation. error: the current error; basin*target - current*level


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL412-L422' class='documenter-source'>source</a><br>

<a id='Ribasim.Pump' href='#Ribasim.Pump'>#</a>
**`Ribasim.Pump`** &mdash; *Type*.



node*id: node ID of the Pump node active: whether this node is active and thus contributes flow flow*rate: target flow rate min*flow*rate: The minimal flow rate of the pump max*flow*rate: The maximum flow rate of the pump control*mapping: dictionary from (node*id, control*state) to target flow rate is*pid_controlled: whether the flow rate of this pump is governed by PID control


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL290-L298' class='documenter-source'>source</a><br>

<a id='Ribasim.TabulatedRatingCurve' href='#Ribasim.TabulatedRatingCurve'>#</a>
**`Ribasim.TabulatedRatingCurve`** &mdash; *Type*.



```julia
struct TabulatedRatingCurve{C}
```

Rating curve from level to discharge. The rating curve is a lookup table with linear interpolation in between. Relation can be updated in time, which is done by moving data from the `time` field into the `tables`, which is done in the `update_tabulated_rating_curve` callback.

Type parameter C indicates the content backing the StructVector, which can be a NamedTuple of Vectors or Arrow Primitives, and is added to avoid type instabilities.

node*id: node ID of the TabulatedRatingCurve node active: whether this node is active and thus contributes flows tables: The current Q(h) relationships time: The time table used for updating the tables control*mapping: dictionary from (node*id, control*state) to Q(h) and/or active state


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL165-L181' class='documenter-source'>source</a><br>

<a id='Ribasim.Terminal' href='#Ribasim.Terminal'>#</a>
**`Ribasim.Terminal`** &mdash; *Type*.



node_id: node ID of the Terminal node


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL379-L381' class='documenter-source'>source</a><br>

<a id='Ribasim.User' href='#Ribasim.User'>#</a>
**`Ribasim.User`** &mdash; *Type*.



demand: water flux demand of user per priority over time active: whether this node is active and thus demands water allocated: water flux currently allocated to user per priority return*factor: the factor in [0,1] of how much of the abstracted water is given back to the system min*level: The level of the source basin below which the user does not abstract priorities: All used priority values. Each user has a demand for all these priorities, which is always 0.0 if it is not provided explicitly.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL433-L441' class='documenter-source'>source</a><br>

<a id='Ribasim.config.Config-Tuple{AbstractString}' href='#Ribasim.config.Config-Tuple{AbstractString}'>#</a>
**`Ribasim.config.Config`** &mdash; *Method*.



```julia
Config(config_path::AbstractString; kwargs...)
```

Parse a TOML file to a Config. Keys can be overruled using keyword arguments. To overrule keys from a subsection, e.g. `dt` from the `solver` section, use underscores: `solver_dt`.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL133-L138' class='documenter-source'>source</a><br>


<a id='Functions'></a>

<a id='Functions-1'></a>

## Functions

<a id='BasicModelInterface.finalize-Tuple{Ribasim.Model}' href='#BasicModelInterface.finalize-Tuple{Ribasim.Model}'>#</a>
**`BasicModelInterface.finalize`** &mdash; *Method*.



```julia
BMI.finalize(model::Model)::Model
```

Write all results to the configured files.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL145-L149' class='documenter-source'>source</a><br>

<a id='BasicModelInterface.initialize-Tuple{Type{Ribasim.Model}, AbstractString}' href='#BasicModelInterface.initialize-Tuple{Type{Ribasim.Model}, AbstractString}'>#</a>
**`BasicModelInterface.initialize`** &mdash; *Method*.



```julia
BMI.initialize(T::Type{Model}, config_path::AbstractString)::Model
```

Initialize a [`Model`](index.md#Ribasim.Model) from the path to the TOML configuration file.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL1-L5' class='documenter-source'>source</a><br>

<a id='BasicModelInterface.initialize-Tuple{Type{Ribasim.Model}, Ribasim.config.Config}' href='#BasicModelInterface.initialize-Tuple{Type{Ribasim.Model}, Ribasim.config.Config}'>#</a>
**`BasicModelInterface.initialize`** &mdash; *Method*.



```julia
BMI.initialize(T::Type{Model}, config::Config)::Model
```

Initialize a [`Model`](index.md#Ribasim.Model) from a [`Config`](index.md#Ribasim.config.Config-Tuple{AbstractString}).


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL11-L15' class='documenter-source'>source</a><br>

<a id='CommonSolve.solve!-Tuple{Ribasim.Model}' href='#CommonSolve.solve!-Tuple{Ribasim.Model}'>#</a>
**`CommonSolve.solve!`** &mdash; *Method*.



```julia
solve!(model::Model)::ODESolution
```

Solve a Model until the configured `endtime`.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/lib.jl#LL51-L55' class='documenter-source'>source</a><br>

<a id='Ribasim.add_constraints_basin_allocation!-Tuple{JuMP.Model, Vector{Int64}}' href='#Ribasim.add_constraints_basin_allocation!-Tuple{JuMP.Model, Vector{Int64}}'>#</a>
**`Ribasim.add_constraints_basin_allocation!`** &mdash; *Method*.



Add the basin allocation constraints to the allocation problem; the allocations to the basins are bounded from above by the basin demand (these are set before each allocation solve). The constraint indices are allocation graph basin node IDs.

Constraint: allocation to basin <= basin demand


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL469-L477' class='documenter-source'>source</a><br>

<a id='Ribasim.add_constraints_capacity!-Tuple{JuMP.Model, SparseArrays.SparseMatrixCSC{Float64, Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}}' href='#Ribasim.add_constraints_capacity!-Tuple{JuMP.Model, SparseArrays.SparseMatrixCSC{Float64, Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}}'>#</a>
**`Ribasim.add_constraints_capacity!`** &mdash; *Method*.



Add the flow capacity constraints to the allocation problem. Only finite capacities get a constraint. The constraint indices are the allocation graph edge IDs.

Constraint: flow over edge <= edge capacity


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL492-L499' class='documenter-source'>source</a><br>

<a id='Ribasim.add_constraints_flow_conservation!-Tuple{JuMP.Model, Vector{Int64}, Dict{Int64, Vector{Int64}}, Dict{Int64, Vector{Int64}}}' href='#Ribasim.add_constraints_flow_conservation!-Tuple{JuMP.Model, Vector{Int64}, Dict{Int64, Vector{Int64}}, Dict{Int64, Vector{Int64}}}'>#</a>
**`Ribasim.add_constraints_flow_conservation!`** &mdash; *Method*.



Add the flow conservation constraints to the allocation problem. The constraint indices are allocgraph user node IDs.

Constraint: sum(flows out of node node) <= flows into node + flow from storage and vertical fluxes


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL548-L554' class='documenter-source'>source</a><br>

<a id='Ribasim.add_constraints_source!-Tuple{JuMP.Model, Dict{Int64, Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Graphs.SimpleGraphs.SimpleDiGraph{Int64}}' href='#Ribasim.add_constraints_source!-Tuple{JuMP.Model, Dict{Int64, Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Graphs.SimpleGraphs.SimpleDiGraph{Int64}}'>#</a>
**`Ribasim.add_constraints_source!`** &mdash; *Method*.



Add the source constraints to the allocation problem. The actual threshold values will be set before each allocation solve. The constraint indices are the allocation graph source node IDs.

Constraint: flow over source edge <= source flow in subnetwork


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL521-L528' class='documenter-source'>source</a><br>

<a id='Ribasim.add_constraints_user_allocation!-Tuple{JuMP.Model, Ribasim.User, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}}' href='#Ribasim.add_constraints_user_allocation!-Tuple{JuMP.Model, Ribasim.User, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}}'>#</a>
**`Ribasim.add_constraints_user_allocation!`** &mdash; *Method*.



Add the user allocation constraints to the allocation problem:

  * The sum of the allocations to a user is equal to the flow to that user;
  * The allocations to the users are non-negative;
  * The allocations to the users are bounded from above by the user demands (these are set before each allocation solve).

The demand constrains have name demand*user*{i} where the i are the allocation graph user node IDs and the constraint indices are the priorities.

Constraints: sum(allocations to user of all priorities) = flow to user allocation to user at priority >= 0 allocation to user at priority <= demand from user at priority


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL423-L437' class='documenter-source'>source</a><br>

<a id='Ribasim.add_constraints_user_returnflow!-Tuple{JuMP.Model, Vector{Int64}}' href='#Ribasim.add_constraints_user_returnflow!-Tuple{JuMP.Model, Vector{Int64}}'>#</a>
**`Ribasim.add_constraints_user_returnflow!`** &mdash; *Method*.



Add the user returnflow constraints to the allocation problem. The constraint indices are allocation graph user node IDs.

Constraint: outflow from user = return factor * inflow to user


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL575-L581' class='documenter-source'>source</a><br>

<a id='Ribasim.add_objective_function!-Tuple{JuMP.Model, Ribasim.User, Vector{Int64}}' href='#Ribasim.add_objective_function!-Tuple{JuMP.Model, Ribasim.User, Vector{Int64}}'>#</a>
**`Ribasim.add_objective_function!`** &mdash; *Method*.



Add the objective function to the allocation problem. Objective function: linear combination of allocations to the basins and users, where     basin allocations get a weight of 1.0 and user allocations get a weight of 2^(-priority index).


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL599-L603' class='documenter-source'>source</a><br>

<a id='Ribasim.add_variables_allocation_basin!-Tuple{JuMP.Model, Dict{Int64, Tuple{Int64, Symbol}}, Vector{Int64}}' href='#Ribasim.add_variables_allocation_basin!-Tuple{JuMP.Model, Dict{Int64, Tuple{Int64, Symbol}}, Vector{Int64}}'>#</a>
**`Ribasim.add_variables_allocation_basin!`** &mdash; *Method*.



Add the basin allocation variables A_basin to the allocation problem. The variable indices are the allocation graph basin node IDs. Non-negativivity constraints are also immediately added to the basin allocation variables.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL409-L413' class='documenter-source'>source</a><br>

<a id='Ribasim.add_variables_allocation_user!-Tuple{JuMP.Model, Ribasim.User, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}}' href='#Ribasim.add_variables_allocation_user!-Tuple{JuMP.Model, Ribasim.User, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}}'>#</a>
**`Ribasim.add_variables_allocation_user!`** &mdash; *Method*.



Add the user allocation variables A*user*{i} to the allocation problem. The variable name indices i are the allocation graph user node IDs, The variable indices are the priorities.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL389-L393' class='documenter-source'>source</a><br>

<a id='Ribasim.add_variables_flow!-Tuple{JuMP.Model, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}}' href='#Ribasim.add_variables_flow!-Tuple{JuMP.Model, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}}'>#</a>
**`Ribasim.add_variables_flow!`** &mdash; *Method*.



Add the flow variables F to the allocation problem. The variable indices are the allocation graph edge IDs. Non-negativivity constraints are also immediately added to the flow variables.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL375-L379' class='documenter-source'>source</a><br>

<a id='Ribasim.allocate!-Tuple{Ribasim.Parameters, Ribasim.AllocationModel, Float64}' href='#Ribasim.allocate!-Tuple{Ribasim.Parameters, Ribasim.AllocationModel, Float64}'>#</a>
**`Ribasim.allocate!`** &mdash; *Method*.



Update the allocation optimization problem for the given subnetwork with the problem state and flows, solve the allocation problem and assign the results to the users.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL820-L823' class='documenter-source'>source</a><br>

<a id='Ribasim.allocation_graph-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}}' href='#Ribasim.allocation_graph-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}}'>#</a>
**`Ribasim.allocation_graph`** &mdash; *Method*.



Build the graph used for the allocation problem.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL301-L303' class='documenter-source'>source</a><br>

<a id='Ribasim.allocation_problem-Tuple{Ribasim.Parameters, Dict{Int64, Tuple{Int64, Symbol}}, Vector{Int64}, Vector{Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}, Dict{Int64, Int64}, Graphs.SimpleGraphs.SimpleDiGraph{Int64}, SparseArrays.SparseMatrixCSC{Float64, Int64}}' href='#Ribasim.allocation_problem-Tuple{Ribasim.Parameters, Dict{Int64, Tuple{Int64, Symbol}}, Vector{Int64}, Vector{Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}, Dict{Int64, Int64}, Graphs.SimpleGraphs.SimpleDiGraph{Int64}, SparseArrays.SparseMatrixCSC{Float64, Int64}}'>#</a>
**`Ribasim.allocation_problem`** &mdash; *Method*.



Construct the allocation problem for the current subnetwork as a JuMP.jl model.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL626-L628' class='documenter-source'>source</a><br>

<a id='Ribasim.assign_allocations!-Tuple{Ribasim.AllocationModel, Ribasim.User}' href='#Ribasim.assign_allocations!-Tuple{Ribasim.AllocationModel, Ribasim.User}'>#</a>
**`Ribasim.assign_allocations!`** &mdash; *Method*.



Assign the allocations to the users as determined by the solution of the allocation problem.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL805-L807' class='documenter-source'>source</a><br>

<a id='Ribasim.avoid_using_own_returnflow!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Vector{Int64}, Dict{Int64, Tuple{Int64, Symbol}}}' href='#Ribasim.avoid_using_own_returnflow!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Vector{Int64}, Dict{Int64, Tuple{Int64, Symbol}}}'>#</a>
**`Ribasim.avoid_using_own_returnflow!`** &mdash; *Method*.



Remove user return flow edges that are upstream of the user itself, and collect the IDs of the allocation graph node IDs of the users that do not have this problem.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL270-L273' class='documenter-source'>source</a><br>

<a id='Ribasim.basin_bottom-Tuple{Ribasim.Basin, Int64}' href='#Ribasim.basin_bottom-Tuple{Ribasim.Basin, Int64}'>#</a>
**`Ribasim.basin_bottom`** &mdash; *Method*.



Return the bottom elevation of the basin with index i, or nothing if it doesn't exist


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL439' class='documenter-source'>source</a><br>

<a id='Ribasim.basin_bottoms-Tuple{Ribasim.Basin, Int64, Int64, Int64}' href='#Ribasim.basin_bottoms-Tuple{Ribasim.Basin, Int64, Int64, Int64}'>#</a>
**`Ribasim.basin_bottoms`** &mdash; *Method*.



Get the bottom on both ends of a node. If only one has a bottom, use that for both.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL452' class='documenter-source'>source</a><br>

<a id='Ribasim.basin_table-Tuple{Ribasim.Model}' href='#Ribasim.basin_table-Tuple{Ribasim.Model}'>#</a>
**`Ribasim.basin_table`** &mdash; *Method*.



Create the basin result table from the saved data


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL171' class='documenter-source'>source</a><br>

<a id='Ribasim.create_callbacks-Tuple{Ribasim.Parameters, Ribasim.config.Config}' href='#Ribasim.create_callbacks-Tuple{Ribasim.Parameters, Ribasim.config.Config}'>#</a>
**`Ribasim.create_callbacks`** &mdash; *Method*.



Create the different callbacks that are used to store results and feed the simulation with new data. The different callbacks are combined to a CallbackSet that goes to the integrator. Returns the CallbackSet and the SavedValues for flow.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL194-L199' class='documenter-source'>source</a><br>

<a id='Ribasim.create_graph-Tuple{SQLite.DB, String}' href='#Ribasim.create_graph-Tuple{SQLite.DB, String}'>#</a>
**`Ribasim.create_graph`** &mdash; *Method*.



Return a directed graph, and a mapping from source and target nodes to edge fid.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL15' class='documenter-source'>source</a><br>

<a id='Ribasim.create_storage_tables-Tuple{SQLite.DB, Ribasim.config.Config}' href='#Ribasim.create_storage_tables-Tuple{SQLite.DB, Ribasim.config.Config}'>#</a>
**`Ribasim.create_storage_tables`** &mdash; *Method*.



Read the Basin / profile table and return all area and level and computed storage values


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL51' class='documenter-source'>source</a><br>

<a id='Ribasim.datetime_since-Tuple{Real, Dates.DateTime}' href='#Ribasim.datetime_since-Tuple{Real, Dates.DateTime}'>#</a>
**`Ribasim.datetime_since`** &mdash; *Method*.



```julia
datetime_since(t::Real, t0::DateTime)::DateTime
```

Convert a Real that represents the seconds passed since the simulation start to the nearest DateTime. This is used to convert between the solver's inner float time, and the calendar.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL39-L44' class='documenter-source'>source</a><br>

<a id='Ribasim.datetimes-Tuple{Ribasim.Model}' href='#Ribasim.datetimes-Tuple{Ribasim.Model}'>#</a>
**`Ribasim.datetimes`** &mdash; *Method*.



Get all saved times as a Vector{DateTime}


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/lib.jl#LL35' class='documenter-source'>source</a><br>

<a id='Ribasim.discrete_control_affect!-Tuple{Any, Int64, Union{Missing, Bool}}' href='#Ribasim.discrete_control_affect!-Tuple{Any, Int64, Union{Missing, Bool}}'>#</a>
**`Ribasim.discrete_control_affect!`** &mdash; *Method*.



Change parameters based on the control logic.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL380-L382' class='documenter-source'>source</a><br>

<a id='Ribasim.discrete_control_affect_downcrossing!-Tuple{Any, Any}' href='#Ribasim.discrete_control_affect_downcrossing!-Tuple{Any, Any}'>#</a>
**`Ribasim.discrete_control_affect_downcrossing!`** &mdash; *Method*.



An downcrossing means that a condition (always greater than) becomes false.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL347-L349' class='documenter-source'>source</a><br>

<a id='Ribasim.discrete_control_affect_upcrossing!-Tuple{Any, Any}' href='#Ribasim.discrete_control_affect_upcrossing!-Tuple{Any, Any}'>#</a>
**`Ribasim.discrete_control_affect_upcrossing!`** &mdash; *Method*.



An upcrossing means that a condition (always greater than) becomes true.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL311-L313' class='documenter-source'>source</a><br>

<a id='Ribasim.discrete_control_condition-NTuple{4, Any}' href='#Ribasim.discrete_control_condition-NTuple{4, Any}'>#</a>
**`Ribasim.discrete_control_condition`** &mdash; *Method*.



Listens for changes in condition truths.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL245-L247' class='documenter-source'>source</a><br>

<a id='Ribasim.discrete_control_table-Tuple{Ribasim.Model}' href='#Ribasim.discrete_control_table-Tuple{Ribasim.Model}'>#</a>
**`Ribasim.discrete_control_table`** &mdash; *Method*.



Create a discrete control result table from the saved data


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL204' class='documenter-source'>source</a><br>

<a id='Ribasim.expand_logic_mapping-Tuple{Dict{Tuple{Int64, String}, String}}' href='#Ribasim.expand_logic_mapping-Tuple{Dict{Tuple{Int64, String}, String}}'>#</a>
**`Ribasim.expand_logic_mapping`** &mdash; *Method*.



Replace the truth states in the logic mapping which contain wildcards with all possible explicit truth states.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL585-L588' class='documenter-source'>source</a><br>

<a id='Ribasim.find_allocation_graph_edges!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Dict{Int64, Tuple{Int64, Symbol}}, Ribasim.Parameters, Vector{Int64}}' href='#Ribasim.find_allocation_graph_edges!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Dict{Int64, Tuple{Int64, Symbol}}, Ribasim.Parameters, Vector{Int64}}'>#</a>
**`Ribasim.find_allocation_graph_edges!`** &mdash; *Method*.



This loop finds allocgraph edges in several ways:

  * Between allocgraph nodes whose equivalent in the subnetwork are directly connected
  * Between allocgraph nodes whose equivalent in the subnetwork are connected with one or more non-junction nodes in between

Here edges are added to the allocation graph that are given by a single edge in the subnetwork.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL63-L71' class='documenter-source'>source</a><br>

<a id='Ribasim.findlastgroup-Tuple{Int64, AbstractVector{Int64}}' href='#Ribasim.findlastgroup-Tuple{Int64, AbstractVector{Int64}}'>#</a>
**`Ribasim.findlastgroup`** &mdash; *Method*.



For an element `id` and a vector of elements `ids`, get the range of indices of the last consecutive block of `id`. Returns the empty range `1:0` if `id` is not in `ids`.

```julia
#                         1 2 3 4 5 6 7 8 9
Ribasim.findlastgroup(2, [5,4,2,2,5,2,2,2,1])
# output
6:8
```


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL211-L222' class='documenter-source'>source</a><br>

<a id='Ribasim.findsorted-Tuple{Any, Any}' href='#Ribasim.findsorted-Tuple{Any, Any}'>#</a>
**`Ribasim.findsorted`** &mdash; *Method*.



Find the index of element x in a sorted collection a. Returns the index of x if it exists, or nothing if it doesn't. If x occurs more than once, throw an error.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL313-L317' class='documenter-source'>source</a><br>

<a id='Ribasim.flow_table-Tuple{Ribasim.Model}' href='#Ribasim.flow_table-Tuple{Ribasim.Model}'>#</a>
**`Ribasim.flow_table`** &mdash; *Method*.



Create a flow result table from the saved data


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL183' class='documenter-source'>source</a><br>

<a id='Ribasim.formulate_basins!-Tuple{AbstractVector, Ribasim.Basin, AbstractMatrix, AbstractVector}' href='#Ribasim.formulate_basins!-Tuple{AbstractVector, Ribasim.Basin, AbstractMatrix, AbstractVector}'>#</a>
**`Ribasim.formulate_basins!`** &mdash; *Method*.



Smoothly let the evaporation flux go to 0 when at small water depths Currently at less than 0.1 m.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL551-L554' class='documenter-source'>source</a><br>

<a id='Ribasim.formulate_flow!-Tuple{Ribasim.LinearResistance, Ribasim.Parameters, AbstractVector, Float64}' href='#Ribasim.formulate_flow!-Tuple{Ribasim.LinearResistance, Ribasim.Parameters, AbstractVector, Float64}'>#</a>
**`Ribasim.formulate_flow!`** &mdash; *Method*.



Directed graph: outflow is positive!


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL816-L818' class='documenter-source'>source</a><br>

<a id='Ribasim.formulate_flow!-Tuple{Ribasim.ManningResistance, Ribasim.Parameters, AbstractVector, Float64}' href='#Ribasim.formulate_flow!-Tuple{Ribasim.ManningResistance, Ribasim.Parameters, AbstractVector, Float64}'>#</a>
**`Ribasim.formulate_flow!`** &mdash; *Method*.



Conservation of energy for two basins, a and b:

```
h_a + v_a^2 / (2 * g) = h_b + v_b^2 / (2 * g) + S_f * L + C / 2 * g * (v_b^2 - v_a^2)
```

Where:

  * h*a, h*b are the heads at basin a and b.
  * v*a, v*b are the velocities at basin a and b.
  * g is the gravitational constant.
  * S_f is the friction slope.
  * C is an expansion or extraction coefficient.

We assume velocity differences are negligible (v*a = v*b):

```
h_a = h_b + S_f * L
```

The friction losses are approximated by the Gauckler-Manning formula:

```
Q = A * (1 / n) * R_h^(2/3) * S_f^(1/2)
```

Where:

  * Where A is the cross-sectional area.
  * V is the cross-sectional average velocity.
  * n is the Gauckler-Manning coefficient.
  * R_h is the hydraulic radius.
  * S_f is the friction slope.

The hydraulic radius is defined as:

```
R_h = A / P
```

Where P is the wetted perimeter.

The average of the upstream and downstream water depth is used to compute cross-sectional area and hydraulic radius. This ensures that a basin can receive water after it has gone dry.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL880-L918' class='documenter-source'>source</a><br>

<a id='Ribasim.formulate_flow!-Tuple{Ribasim.TabulatedRatingCurve, Ribasim.Parameters, AbstractVector, Float64}' href='#Ribasim.formulate_flow!-Tuple{Ribasim.TabulatedRatingCurve, Ribasim.Parameters, AbstractVector, Float64}'>#</a>
**`Ribasim.formulate_flow!`** &mdash; *Method*.



Directed graph: outflow is positive!


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL846-L848' class='documenter-source'>source</a><br>

<a id='Ribasim.get_area_and_level-Tuple{Ribasim.Basin, Int64, Real}' href='#Ribasim.get_area_and_level-Tuple{Ribasim.Basin, Int64, Real}'>#</a>
**`Ribasim.get_area_and_level`** &mdash; *Method*.



Compute the area and level of a basin given its storage. Also returns darea/dlevel as it is needed for the Jacobian.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL123-L126' class='documenter-source'>source</a><br>

<a id='Ribasim.get_compressor-Tuple{Ribasim.config.Results}' href='#Ribasim.get_compressor-Tuple{Ribasim.config.Results}'>#</a>
**`Ribasim.get_compressor`** &mdash; *Method*.



Get the compressor based on the Results section


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL469' class='documenter-source'>source</a><br>

<a id='Ribasim.get_fractional_flow_connected_basins-Tuple{Int64, Ribasim.Basin, Ribasim.FractionalFlow, Graphs.SimpleGraphs.SimpleDiGraph{Int64}}' href='#Ribasim.get_fractional_flow_connected_basins-Tuple{Int64, Ribasim.Basin, Ribasim.FractionalFlow, Graphs.SimpleGraphs.SimpleDiGraph{Int64}}'>#</a>
**`Ribasim.get_fractional_flow_connected_basins`** &mdash; *Method*.



Get the node type specific indices of the fractional flows and basins, that are consecutively connected to a node of given id.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL847-L850' class='documenter-source'>source</a><br>

<a id='Ribasim.get_jac_prototype-Tuple{Ribasim.Parameters}' href='#Ribasim.get_jac_prototype-Tuple{Ribasim.Parameters}'>#</a>
**`Ribasim.get_jac_prototype`** &mdash; *Method*.



Get a sparse matrix whose sparsity matches the sparsity of the Jacobian of the ODE problem. All nodes are taken into consideration, also the ones that are inactive.

In Ribasim the Jacobian is typically sparse because each state only depends on a small number of other states.

Note: the name 'prototype' does not mean this code is a prototype, it comes from the naming convention of this sparsity structure in the differentialequations.jl docs.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL643-L654' class='documenter-source'>source</a><br>

<a id='Ribasim.get_level-Tuple{Ribasim.Parameters, Int64, Float64}' href='#Ribasim.get_level-Tuple{Ribasim.Parameters, Int64, Float64}'>#</a>
**`Ribasim.get_level`** &mdash; *Method*.



Get the current water level of a node ID. The ID can belong to either a Basin or a LevelBoundary. storage: tells ForwardDiff whether this call is for differentiation or not


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL405-L409' class='documenter-source'>source</a><br>

<a id='Ribasim.get_node_id_mapping-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}}' href='#Ribasim.get_node_id_mapping-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}}'>#</a>
**`Ribasim.get_node_id_mapping`** &mdash; *Method*.



Get:

  * The mapping from subnetwork node IDs to allocation graph node IDs
  * The mapping from allocation graph source node IDs to subnetwork source edge IDs


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL1-L5' class='documenter-source'>source</a><br>

<a id='Ribasim.get_node_in_out_edges-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}}' href='#Ribasim.get_node_in_out_edges-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}}'>#</a>
**`Ribasim.get_node_in_out_edges`** &mdash; *Method*.



Get two dictionaries, where:

  * The first one gives the IDs of the inedges for each node ID in the graph
  * The second one gives the IDs of the outedges for each node ID in the graph


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL949-L953' class='documenter-source'>source</a><br>

<a id='Ribasim.get_scalar_interpolation-Tuple{Dates.DateTime, Float64, AbstractVector, Int64, Symbol}' href='#Ribasim.get_scalar_interpolation-Tuple{Dates.DateTime, Float64, AbstractVector, Int64, Symbol}'>#</a>
**`Ribasim.get_scalar_interpolation`** &mdash; *Method*.



Linear interpolation of a scalar with constant extrapolation.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL238' class='documenter-source'>source</a><br>

<a id='Ribasim.get_storage_from_level-Tuple{Ribasim.Basin, Int64, Float64}' href='#Ribasim.get_storage_from_level-Tuple{Ribasim.Basin, Int64, Float64}'>#</a>
**`Ribasim.get_storage_from_level`** &mdash; *Method*.



Get the storage of a basin from its level.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL72' class='documenter-source'>source</a><br>

<a id='Ribasim.get_storages_and_levels-Tuple{Ribasim.Model}' href='#Ribasim.get_storages_and_levels-Tuple{Ribasim.Model}'>#</a>
**`Ribasim.get_storages_and_levels`** &mdash; *Method*.



Get the storage and level of all basins as matrices of nbasin × ntime


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL148' class='documenter-source'>source</a><br>

<a id='Ribasim.get_storages_from_levels-Tuple{Ribasim.Basin, Vector}' href='#Ribasim.get_storages_from_levels-Tuple{Ribasim.Basin, Vector}'>#</a>
**`Ribasim.get_storages_from_levels`** &mdash; *Method*.



Compute the storages of the basins based on the water level of the basins.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL110' class='documenter-source'>source</a><br>

<a id='Ribasim.get_tstops-Tuple{Any, Dates.DateTime}' href='#Ribasim.get_tstops-Tuple{Any, Dates.DateTime}'>#</a>
**`Ribasim.get_tstops`** &mdash; *Method*.



From an iterable of DateTimes, find the times the solver needs to stop


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL399' class='documenter-source'>source</a><br>

<a id='Ribasim.get_value-Tuple{Ribasim.Parameters, Int64, String, Float64, AbstractVector{Float64}, Float64}' href='#Ribasim.get_value-Tuple{Ribasim.Parameters, Int64, String, Float64, AbstractVector{Float64}, Float64}'>#</a>
**`Ribasim.get_value`** &mdash; *Method*.



Get a value for a condition. Currently supports getting levels from basins and flows from flow boundaries.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL266-L269' class='documenter-source'>source</a><br>

<a id='Ribasim.id_index-Tuple{Dictionaries.Indices{Int64}, Int64}' href='#Ribasim.id_index-Tuple{Dictionaries.Indices{Int64}, Int64}'>#</a>
**`Ribasim.id_index`** &mdash; *Method*.



Get the index of an ID in a set of indices.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL431' class='documenter-source'>source</a><br>

<a id='Ribasim.input_path-Tuple{Ribasim.config.Config, String}' href='#Ribasim.input_path-Tuple{Ribasim.config.Config, String}'>#</a>
**`Ribasim.input_path`** &mdash; *Method*.



Construct a path relative to both the TOML directory and the optional `input_dir`


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL123' class='documenter-source'>source</a><br>

<a id='Ribasim.is_flow_constraining-Tuple{Ribasim.AbstractParameterNode}' href='#Ribasim.is_flow_constraining-Tuple{Ribasim.AbstractParameterNode}'>#</a>
**`Ribasim.is_flow_constraining`** &mdash; *Method*.



Whether the given node node is flow constraining by having a maximum flow rate.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL922' class='documenter-source'>source</a><br>

<a id='Ribasim.is_flow_direction_constraining-Tuple{Ribasim.AbstractParameterNode}' href='#Ribasim.is_flow_direction_constraining-Tuple{Ribasim.AbstractParameterNode}'>#</a>
**`Ribasim.is_flow_direction_constraining`** &mdash; *Method*.



Whether the given node is flow direction constraining (only in direction of edges).


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL925' class='documenter-source'>source</a><br>

<a id='Ribasim.load_data-Tuple{SQLite.DB, Ribasim.config.Config, Type{<:Legolas.AbstractRecord}}' href='#Ribasim.load_data-Tuple{SQLite.DB, Ribasim.config.Config, Type{<:Legolas.AbstractRecord}}'>#</a>
**`Ribasim.load_data`** &mdash; *Method*.



```julia
load_data(db::DB, config::Config, nodetype::Symbol, kind::Symbol)::Union{Table, Query, Nothing}
```

Load data from Arrow files if available, otherwise the database. Returns either an `Arrow.Table`, `SQLite.Query` or `nothing` if the data is not present.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL47-L52' class='documenter-source'>source</a><br>

<a id='Ribasim.load_structvector-Union{Tuple{T}, Tuple{SQLite.DB, Ribasim.config.Config, Type{T}}} where T<:Tables.AbstractRow' href='#Ribasim.load_structvector-Union{Tuple{T}, Tuple{SQLite.DB, Ribasim.config.Config, Type{T}}} where T<:Tables.AbstractRow'>#</a>
**`Ribasim.load_structvector`** &mdash; *Method*.



```julia
load_structvector(db::DB, config::Config, ::Type{T})::StructVector{T}
```

Load data from Arrow files if available, otherwise the database. Always returns a StructVector of the given struct type T, which is empty if the table is not found. This function validates the schema, and enforces the required sort order.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL78-L84' class='documenter-source'>source</a><br>

<a id='Ribasim.nodefields-Tuple{Ribasim.Parameters}' href='#Ribasim.nodefields-Tuple{Ribasim.Parameters}'>#</a>
**`Ribasim.nodefields`** &mdash; *Method*.



Get all node fieldnames of the parameter object.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL637' class='documenter-source'>source</a><br>

<a id='Ribasim.nodetype-Union{Tuple{Legolas.SchemaVersion{T, N}}, Tuple{N}, Tuple{T}} where {T, N}' href='#Ribasim.nodetype-Union{Tuple{Legolas.SchemaVersion{T, N}}, Tuple{N}, Tuple{T}} where {T, N}'>#</a>
**`Ribasim.nodetype`** &mdash; *Method*.



From a SchemaVersion("ribasim.flowboundary.static", 1) return (:FlowBoundary, :static)


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/validation.jl#LL36-L38' class='documenter-source'>source</a><br>

<a id='Ribasim.parse_static_and_time-Tuple{SQLite.DB, Ribasim.config.Config, String}' href='#Ribasim.parse_static_and_time-Tuple{SQLite.DB, Ribasim.config.Config, String}'>#</a>
**`Ribasim.parse_static_and_time`** &mdash; *Method*.



Process the data in the static and time tables for a given node type. The 'defaults' named tuple dictates how missing data is filled in. 'time_interpolatables' is a vector of Symbols of parameter names for which a time interpolation (linear) object must be constructed. The control mapping for DiscreteControl is also constructed in this function. This function currently does not support node states that are defined by more than one row in a table, as is the case for TabulatedRatingCurve.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/create.jl#LL1-L9' class='documenter-source'>source</a><br>

<a id='Ribasim.path_exists_in_graph-Tuple{Graphs.SimpleGraphs.SimpleDiGraph, Int64, Int64}' href='#Ribasim.path_exists_in_graph-Tuple{Graphs.SimpleGraphs.SimpleDiGraph, Int64, Int64}'>#</a>
**`Ribasim.path_exists_in_graph`** &mdash; *Method*.



Find out whether a path exists between a start node and end node in the given graph.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL929' class='documenter-source'>source</a><br>

<a id='Ribasim.process_allocation_graph_edges!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, SparseArrays.SparseMatrixCSC{Float64, Int64}, Vector{Vector{Int64}}, Dict{Int64, Tuple{Int64, Symbol}}, Ribasim.Parameters}' href='#Ribasim.process_allocation_graph_edges!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, SparseArrays.SparseMatrixCSC{Float64, Int64}, Vector{Vector{Int64}}, Dict{Int64, Tuple{Int64, Symbol}}, Ribasim.Parameters}'>#</a>
**`Ribasim.process_allocation_graph_edges!`** &mdash; *Method*.



For the composite allocgraph edges:

  * Find out whether they are connected to allocgraph nodes on both ends
  * Compute their capacity
  * Find out their allowed flow direction(s)


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL146-L151' class='documenter-source'>source</a><br>

<a id='Ribasim.profile_storage-Tuple{Vector, Vector}' href='#Ribasim.profile_storage-Tuple{Vector, Vector}'>#</a>
**`Ribasim.profile_storage`** &mdash; *Method*.



Calculate a profile storage by integrating the areas over the levels


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL36' class='documenter-source'>source</a><br>

<a id='Ribasim.qh_interpolation-Tuple{Int64, StructArrays.StructVector}' href='#Ribasim.qh_interpolation-Tuple{Int64, StructArrays.StructVector}'>#</a>
**`Ribasim.qh_interpolation`** &mdash; *Method*.



From a table with columns node*id, discharge (Q) and level (h), create a LinearInterpolation from level to discharge for a given node*id.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL300-L303' class='documenter-source'>source</a><br>

<a id='Ribasim.reduction_factor-Union{Tuple{T}, Tuple{T, Real}} where T<:Real' href='#Ribasim.reduction_factor-Union{Tuple{T}, Tuple{T, Real}} where T<:Real'>#</a>
**`Ribasim.reduction_factor`** &mdash; *Method*.



Function that goes smoothly from 0 to 1 in the interval [0,threshold], and is constant outside this interval.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL907-L910' class='documenter-source'>source</a><br>

<a id='Ribasim.results_path-Tuple{Ribasim.config.Config, String}' href='#Ribasim.results_path-Tuple{Ribasim.config.Config, String}'>#</a>
**`Ribasim.results_path`** &mdash; *Method*.



Construct a path relative to both the TOML directory and the optional `results_dir`


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL128' class='documenter-source'>source</a><br>

<a id='Ribasim.run-Tuple{AbstractString}' href='#Ribasim.run-Tuple{AbstractString}'>#</a>
**`Ribasim.run`** &mdash; *Method*.



```julia
run(config_file::AbstractString)::Model
run(config::Config)::Model
```

Run a [`Model`](index.md#Ribasim.Model), given a path to a TOML configuration file, or a Config object. Running a model includes initialization, solving to the end with `[`solve!`](@ref)` and writing results with [`BMI.finalize`](index.md#BasicModelInterface.finalize-Tuple{Ribasim.Model}).


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL571-L577' class='documenter-source'>source</a><br>

<a id='Ribasim.save_flow-Tuple{Any, Any, Any}' href='#Ribasim.save_flow-Tuple{Any, Any, Any}'>#</a>
**`Ribasim.save_flow`** &mdash; *Method*.



Copy the current flow to the SavedValues


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL474' class='documenter-source'>source</a><br>

<a id='Ribasim.scalar_interpolation_derivative-Tuple{DataInterpolations.LinearInterpolation{Vector{Float64}, Vector{Float64}, true, Float64}, Float64}' href='#Ribasim.scalar_interpolation_derivative-Tuple{DataInterpolations.LinearInterpolation{Vector{Float64}, Vector{Float64}, true, Float64}, Float64}'>#</a>
**`Ribasim.scalar_interpolation_derivative`** &mdash; *Method*.



Derivative of scalar interpolation.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL265' class='documenter-source'>source</a><br>

<a id='Ribasim.seconds_since-Tuple{Dates.DateTime, Dates.DateTime}' href='#Ribasim.seconds_since-Tuple{Dates.DateTime, Dates.DateTime}'>#</a>
**`Ribasim.seconds_since`** &mdash; *Method*.



```julia
seconds_since(t::DateTime, t0::DateTime)::Float64
```

Convert a DateTime to a float that is the number of seconds since the start of the simulation. This is used to convert between the solver's inner float time, and the calendar.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL31-L36' class='documenter-source'>source</a><br>

<a id='Ribasim.set_current_value!-Tuple{NamedTuple, Vector{Int64}, StructArrays.StructVector, Dates.DateTime}' href='#Ribasim.set_current_value!-Tuple{NamedTuple, Vector{Int64}, StructArrays.StructVector, Dates.DateTime}'>#</a>
**`Ribasim.set_current_value!`** &mdash; *Method*.



From a timeseries table `time`, load the most recent applicable data into `table`. `table` must be a NamedTuple of vectors with all variables that must be loaded. The most recent applicable data is non-NaN data for a given ID that is on or before `t`.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL363-L367' class='documenter-source'>source</a><br>

<a id='Ribasim.set_model_state_in_allocation!-Tuple{Ribasim.AllocationModel, Ribasim.Parameters, Float64}' href='#Ribasim.set_model_state_in_allocation!-Tuple{Ribasim.AllocationModel, Ribasim.Parameters, Float64}'>#</a>
**`Ribasim.set_model_state_in_allocation!`** &mdash; *Method*.



Update the allocation problem with model data at the current:

  * Demands of the users
  * Flows of the source edges
  * Demands of the basins


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL756-L761' class='documenter-source'>source</a><br>

<a id='Ribasim.set_static_value!-Tuple{NamedTuple, Vector{Int64}, StructArrays.StructVector}' href='#Ribasim.set_static_value!-Tuple{NamedTuple, Vector{Int64}, StructArrays.StructVector}'>#</a>
**`Ribasim.set_static_value!`** &mdash; *Method*.



Load data from a source table `static` into a destination `table`. Data is matched based on the node_id, which is sorted.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL345-L348' class='documenter-source'>source</a><br>

<a id='Ribasim.set_table_row!-Tuple{NamedTuple, Any, Int64}' href='#Ribasim.set_table_row!-Tuple{NamedTuple, Any, Int64}'>#</a>
**`Ribasim.set_table_row!`** &mdash; *Method*.



Update `table` at row index `i`, with the values of a given row. `table` must be a NamedTuple of vectors with all variables that must be loaded. The row must contain all the column names that are present in the table. If a value is NaN, it is not set.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL329-L334' class='documenter-source'>source</a><br>

<a id='Ribasim.sorted_table!-Tuple{StructArrays.StructVector{<:Legolas.AbstractRecord}}' href='#Ribasim.sorted_table!-Tuple{StructArrays.StructVector{<:Legolas.AbstractRecord}}'>#</a>
**`Ribasim.sorted_table!`** &mdash; *Method*.



Depending on if a table can be sorted, either sort it or assert that it is sorted.

Tables loaded from the database into memory can be sorted. Tables loaded from Arrow files are memory mapped and can therefore not be sorted.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/validation.jl#LL388-L393' class='documenter-source'>source</a><br>

<a id='Ribasim.timesteps-Tuple{Ribasim.Model}' href='#Ribasim.timesteps-Tuple{Ribasim.Model}'>#</a>
**`Ribasim.timesteps`** &mdash; *Method*.



Get all saved times in seconds since start


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/lib.jl#LL32' class='documenter-source'>source</a><br>

<a id='Ribasim.update_allocation!-Tuple{Any}' href='#Ribasim.update_allocation!-Tuple{Any}'>#</a>
**`Ribasim.update_allocation!`** &mdash; *Method*.



Solve the allocation problem for all users and assign allocated abstractions to user nodes.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL504' class='documenter-source'>source</a><br>

<a id='Ribasim.update_basin-Tuple{Any}' href='#Ribasim.update_basin-Tuple{Any}'>#</a>
**`Ribasim.update_basin`** &mdash; *Method*.



Load updates from 'Basin / time' into the parameters


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL479' class='documenter-source'>source</a><br>

<a id='Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Ribasim.AbstractParameterNode}' href='#Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Ribasim.AbstractParameterNode}'>#</a>
**`Ribasim.update_jac_prototype!`** &mdash; *Method*.



Method for nodes that do not contribute to the Jacobian


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL705-L707' class='documenter-source'>source</a><br>

<a id='Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Ribasim.PidControl}' href='#Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Ribasim.PidControl}'>#</a>
**`Ribasim.update_jac_prototype!`** &mdash; *Method*.



The controlled basin affects itself and the basins upstream and downstream of the controlled pump affect eachother if there is a basin upstream of the pump. The state for the integral term and the controlled basin affect eachother, and the same for the integral state and the basin upstream of the pump if it is indeed a basin.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL783-L788' class='documenter-source'>source</a><br>

<a id='Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Union{Ribasim.LinearResistance, Ribasim.ManningResistance}}' href='#Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Union{Ribasim.LinearResistance, Ribasim.ManningResistance}}'>#</a>
**`Ribasim.update_jac_prototype!`** &mdash; *Method*.



If both the unique node upstream and the unique node downstream of these nodes are basins, then these directly depend on eachother and affect the Jacobian 2x Basins always depend on themselves.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL669-L673' class='documenter-source'>source</a><br>

<a id='Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Union{Ribasim.User, Ribasim.Outlet, Ribasim.Pump, Ribasim.TabulatedRatingCurve}}' href='#Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Union{Ribasim.User, Ribasim.Outlet, Ribasim.Pump, Ribasim.TabulatedRatingCurve}}'>#</a>
**`Ribasim.update_jac_prototype!`** &mdash; *Method*.



If both the unique node upstream and the nodes down stream (or one node further if a fractional flow is in between) are basins, then the downstream basin depends on the upstream basin(s) and affect the Jacobian as many times as there are downstream basins Upstream basins always depend on themselves.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL733-L738' class='documenter-source'>source</a><br>

<a id='Ribasim.update_tabulated_rating_curve!-Tuple{Any}' href='#Ribasim.update_tabulated_rating_curve!-Tuple{Any}'>#</a>
**`Ribasim.update_tabulated_rating_curve!`** &mdash; *Method*.



Load updates from 'TabulatedRatingCurve / time' into the parameters


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/bmi.jl#LL512' class='documenter-source'>source</a><br>

<a id='Ribasim.valid_discrete_control-Tuple{Ribasim.Parameters, Ribasim.config.Config}' href='#Ribasim.valid_discrete_control-Tuple{Ribasim.Parameters, Ribasim.config.Config}'>#</a>
**`Ribasim.valid_discrete_control`** &mdash; *Method*.



Check:

  * whether control states are defined for discrete controlled nodes;
  * Whether the supplied truth states have the proper length;
  * Whether look_ahead is only supplied for condition variables given by a time-series.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL484-L489' class='documenter-source'>source</a><br>

<a id='Ribasim.valid_edge_types-Tuple{SQLite.DB}' href='#Ribasim.valid_edge_types-Tuple{SQLite.DB}'>#</a>
**`Ribasim.valid_edge_types`** &mdash; *Method*.



Check that only supported edge types are declared.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/utils.jl#LL1' class='documenter-source'>source</a><br>

<a id='Ribasim.valid_edges-Tuple{Dictionaries.Dictionary{Tuple{Int64, Int64}, Int64}, Dictionaries.Dictionary{Int64, Tuple{Symbol, Symbol}}}' href='#Ribasim.valid_edges-Tuple{Dictionaries.Dictionary{Tuple{Int64, Int64}, Int64}, Dictionaries.Dictionary{Int64, Tuple{Symbol, Symbol}}}'>#</a>
**`Ribasim.valid_edges`** &mdash; *Method*.



Test for each node given its node type whether the nodes that

**are downstream ('down-edge') of this node are of an allowed type**


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/validation.jl#LL409-L412' class='documenter-source'>source</a><br>

<a id='Ribasim.valid_flow_rates-Tuple{Vector{Int64}, Vector, Dict{Tuple{Int64, String}, NamedTuple}, Symbol}' href='#Ribasim.valid_flow_rates-Tuple{Vector{Int64}, Vector, Dict{Tuple{Int64, String}, NamedTuple}, Symbol}'>#</a>
**`Ribasim.valid_flow_rates`** &mdash; *Method*.



Test whether static or discrete controlled flow rates are indeed non-negative.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/validation.jl#LL468-L470' class='documenter-source'>source</a><br>

<a id='Ribasim.valid_fractional_flow-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Vector{Int64}, Vector{Float64}}' href='#Ribasim.valid_fractional_flow-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Vector{Int64}, Vector{Float64}}'>#</a>
**`Ribasim.valid_fractional_flow`** &mdash; *Method*.



Check that nodes that have fractional flow outneighbors do not have any other type of outneighbor, that the fractions leaving a node add up to ≈1 and that the fractions are non-negative.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/validation.jl#LL545-L548' class='documenter-source'>source</a><br>

<a id='Ribasim.valid_n_neighbors-Tuple{Ribasim.Parameters}' href='#Ribasim.valid_n_neighbors-Tuple{Ribasim.Parameters}'>#</a>
**`Ribasim.valid_n_neighbors`** &mdash; *Method*.



Test for each node given its node type whether it has an allowed number of flow/control inneighbors and outneighbors


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL472-L475' class='documenter-source'>source</a><br>

<a id='Ribasim.valid_profiles-Tuple{Dictionaries.Indices{Int64}, Vector{Vector{Float64}}, Vector{Vector{Float64}}}' href='#Ribasim.valid_profiles-Tuple{Dictionaries.Indices{Int64}, Vector{Vector{Float64}}, Vector{Vector{Float64}}}'>#</a>
**`Ribasim.valid_profiles`** &mdash; *Method*.



Check whether the profile data has no repeats in the levels and the areas start positive.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/validation.jl#LL436-L438' class='documenter-source'>source</a><br>

<a id='Ribasim.valid_sources-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Dict{Int64, Tuple{Int64, Symbol}}}' href='#Ribasim.valid_sources-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Dict{Int64, Tuple{Int64, Symbol}}}'>#</a>
**`Ribasim.valid_sources`** &mdash; *Method*.



The source nodes must only have one outneighbor.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/allocation.jl#LL247-L249' class='documenter-source'>source</a><br>

<a id='Ribasim.water_balance!-Tuple{ComponentArrays.ComponentVector, ComponentArrays.ComponentVector, Ribasim.Parameters, Float64}' href='#Ribasim.water_balance!-Tuple{ComponentArrays.ComponentVector, ComponentArrays.ComponentVector, Ribasim.Parameters, Float64}'>#</a>
**`Ribasim.water_balance!`** &mdash; *Method*.



The right hand side function of the system of ODEs set up by Ribasim.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/solve.jl#LL1198-L1200' class='documenter-source'>source</a><br>

<a id='Ribasim.write_arrow-Tuple{AbstractString, NamedTuple, TranscodingStreams.Codec}' href='#Ribasim.write_arrow-Tuple{AbstractString, NamedTuple, TranscodingStreams.Codec}'>#</a>
**`Ribasim.write_arrow`** &mdash; *Method*.



Write a result table to disk as an Arrow file


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/io.jl#LL213' class='documenter-source'>source</a><br>

<a id='Ribasim.config.algorithm-Tuple{Ribasim.config.Solver}' href='#Ribasim.config.algorithm-Tuple{Ribasim.config.Solver}'>#</a>
**`Ribasim.config.algorithm`** &mdash; *Method*.



Create an OrdinaryDiffEqAlgorithm from solver config


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/config.jl#LL216' class='documenter-source'>source</a><br>

<a id='Ribasim.config.snake_case-Tuple{AbstractString}' href='#Ribasim.config.snake_case-Tuple{AbstractString}'>#</a>
**`Ribasim.config.snake_case`** &mdash; *Method*.



Convert a string from CamelCase to snake_case.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/config.jl#LL35' class='documenter-source'>source</a><br>


<a id='Constants'></a>

<a id='Constants-1'></a>

## Constants

<a id='Ribasim.config.algorithms' href='#Ribasim.config.algorithms'>#</a>
**`Ribasim.config.algorithms`** &mdash; *Constant*.



```julia
const algorithms::Dict{String, Type}
```

Map from config string to a supported algorithm type from [OrdinaryDiffEq](https://docs.sciml.ai/DiffEqDocs/stable/solvers/ode_solve/).

Supported algorithms:

  * `QNDF`
  * `Rosenbrock23`
  * `TRBDF2`
  * `Rodas5`
  * `KenCarp4`
  * `Tsit5`
  * `RK4`
  * `ImplicitEuler`
  * `Euler`


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/config.jl#LL187-L203' class='documenter-source'>source</a><br>


<a id='Macros'></a>

<a id='Macros-1'></a>

## Macros

<a id='Ribasim.config.@addfields-Tuple{Expr, Any}' href='#Ribasim.config.@addfields-Tuple{Expr, Any}'>#</a>
**`Ribasim.config.@addfields`** &mdash; *Macro*.



Add fieldnames with Union{String, Nothing} type to struct expression. Requires @option use before it.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/config.jl#LL43-L45' class='documenter-source'>source</a><br>

<a id='Ribasim.config.@addnodetypes-Tuple{Expr}' href='#Ribasim.config.@addnodetypes-Tuple{Expr}'>#</a>
**`Ribasim.config.@addnodetypes`** &mdash; *Macro*.



Add all TableOption subtypes as fields to struct expression. Requires @option use before it.


<a target='_blank' href='https://github.com/Deltares/Ribasim.jl/blob/98014f3c51fdc2efad74f19b6c3b28b5d93bf136/core/src/config.jl#LL56-L58' class='documenter-source'>source</a><br>


<a id='Index'></a>

<a id='Index-1'></a>

## Index

- [`Ribasim.Ribasim`](index.md#Ribasim.Ribasim)
- [`Ribasim.config`](index.md#Ribasim.config)
- [`Ribasim.config.algorithms`](index.md#Ribasim.config.algorithms)
- [`Ribasim.AllocationModel`](index.md#Ribasim.AllocationModel-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}, Float64})
- [`Ribasim.AllocationModel`](index.md#Ribasim.AllocationModel)
- [`Ribasim.Basin`](index.md#Ribasim.Basin)
- [`Ribasim.Connectivity`](index.md#Ribasim.Connectivity)
- [`Ribasim.DiscreteControl`](index.md#Ribasim.DiscreteControl)
- [`Ribasim.FlatVector`](index.md#Ribasim.FlatVector)
- [`Ribasim.FlowBoundary`](index.md#Ribasim.FlowBoundary)
- [`Ribasim.FractionalFlow`](index.md#Ribasim.FractionalFlow)
- [`Ribasim.LevelBoundary`](index.md#Ribasim.LevelBoundary)
- [`Ribasim.LinearResistance`](index.md#Ribasim.LinearResistance)
- [`Ribasim.ManningResistance`](index.md#Ribasim.ManningResistance)
- [`Ribasim.Model`](index.md#Ribasim.Model)
- [`Ribasim.Outlet`](index.md#Ribasim.Outlet)
- [`Ribasim.PidControl`](index.md#Ribasim.PidControl)
- [`Ribasim.Pump`](index.md#Ribasim.Pump)
- [`Ribasim.TabulatedRatingCurve`](index.md#Ribasim.TabulatedRatingCurve)
- [`Ribasim.Terminal`](index.md#Ribasim.Terminal)
- [`Ribasim.User`](index.md#Ribasim.User)
- [`Ribasim.config.Config`](index.md#Ribasim.config.Config-Tuple{AbstractString})
- [`BasicModelInterface.finalize`](index.md#BasicModelInterface.finalize-Tuple{Ribasim.Model})
- [`BasicModelInterface.initialize`](index.md#BasicModelInterface.initialize-Tuple{Type{Ribasim.Model}, AbstractString})
- [`BasicModelInterface.initialize`](index.md#BasicModelInterface.initialize-Tuple{Type{Ribasim.Model}, Ribasim.config.Config})
- [`CommonSolve.solve!`](index.md#CommonSolve.solve!-Tuple{Ribasim.Model})
- [`Ribasim.add_constraints_basin_allocation!`](index.md#Ribasim.add_constraints_basin_allocation!-Tuple{JuMP.Model, Vector{Int64}})
- [`Ribasim.add_constraints_capacity!`](index.md#Ribasim.add_constraints_capacity!-Tuple{JuMP.Model, SparseArrays.SparseMatrixCSC{Float64, Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}})
- [`Ribasim.add_constraints_flow_conservation!`](index.md#Ribasim.add_constraints_flow_conservation!-Tuple{JuMP.Model, Vector{Int64}, Dict{Int64, Vector{Int64}}, Dict{Int64, Vector{Int64}}})
- [`Ribasim.add_constraints_source!`](index.md#Ribasim.add_constraints_source!-Tuple{JuMP.Model, Dict{Int64, Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Graphs.SimpleGraphs.SimpleDiGraph{Int64}})
- [`Ribasim.add_constraints_user_allocation!`](index.md#Ribasim.add_constraints_user_allocation!-Tuple{JuMP.Model, Ribasim.User, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}})
- [`Ribasim.add_constraints_user_returnflow!`](index.md#Ribasim.add_constraints_user_returnflow!-Tuple{JuMP.Model, Vector{Int64}})
- [`Ribasim.add_objective_function!`](index.md#Ribasim.add_objective_function!-Tuple{JuMP.Model, Ribasim.User, Vector{Int64}})
- [`Ribasim.add_variables_allocation_basin!`](index.md#Ribasim.add_variables_allocation_basin!-Tuple{JuMP.Model, Dict{Int64, Tuple{Int64, Symbol}}, Vector{Int64}})
- [`Ribasim.add_variables_allocation_user!`](index.md#Ribasim.add_variables_allocation_user!-Tuple{JuMP.Model, Ribasim.User, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}})
- [`Ribasim.add_variables_flow!`](index.md#Ribasim.add_variables_flow!-Tuple{JuMP.Model, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}})
- [`Ribasim.allocate!`](index.md#Ribasim.allocate!-Tuple{Ribasim.Parameters, Ribasim.AllocationModel, Float64})
- [`Ribasim.allocation_graph`](index.md#Ribasim.allocation_graph-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}})
- [`Ribasim.allocation_problem`](index.md#Ribasim.allocation_problem-Tuple{Ribasim.Parameters, Dict{Int64, Tuple{Int64, Symbol}}, Vector{Int64}, Vector{Int64}, Vector{Graphs.SimpleGraphs.SimpleEdge{Int64}}, Vector{Int64}, Dict{Int64, Int64}, Graphs.SimpleGraphs.SimpleDiGraph{Int64}, SparseArrays.SparseMatrixCSC{Float64, Int64}})
- [`Ribasim.assign_allocations!`](index.md#Ribasim.assign_allocations!-Tuple{Ribasim.AllocationModel, Ribasim.User})
- [`Ribasim.avoid_using_own_returnflow!`](index.md#Ribasim.avoid_using_own_returnflow!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Vector{Int64}, Dict{Int64, Tuple{Int64, Symbol}}})
- [`Ribasim.basin_bottom`](index.md#Ribasim.basin_bottom-Tuple{Ribasim.Basin, Int64})
- [`Ribasim.basin_bottoms`](index.md#Ribasim.basin_bottoms-Tuple{Ribasim.Basin, Int64, Int64, Int64})
- [`Ribasim.basin_table`](index.md#Ribasim.basin_table-Tuple{Ribasim.Model})
- [`Ribasim.config.algorithm`](index.md#Ribasim.config.algorithm-Tuple{Ribasim.config.Solver})
- [`Ribasim.config.snake_case`](index.md#Ribasim.config.snake_case-Tuple{AbstractString})
- [`Ribasim.create_callbacks`](index.md#Ribasim.create_callbacks-Tuple{Ribasim.Parameters, Ribasim.config.Config})
- [`Ribasim.create_graph`](index.md#Ribasim.create_graph-Tuple{SQLite.DB, String})
- [`Ribasim.create_storage_tables`](index.md#Ribasim.create_storage_tables-Tuple{SQLite.DB, Ribasim.config.Config})
- [`Ribasim.datetime_since`](index.md#Ribasim.datetime_since-Tuple{Real, Dates.DateTime})
- [`Ribasim.datetimes`](index.md#Ribasim.datetimes-Tuple{Ribasim.Model})
- [`Ribasim.discrete_control_affect!`](index.md#Ribasim.discrete_control_affect!-Tuple{Any, Int64, Union{Missing, Bool}})
- [`Ribasim.discrete_control_affect_downcrossing!`](index.md#Ribasim.discrete_control_affect_downcrossing!-Tuple{Any, Any})
- [`Ribasim.discrete_control_affect_upcrossing!`](index.md#Ribasim.discrete_control_affect_upcrossing!-Tuple{Any, Any})
- [`Ribasim.discrete_control_condition`](index.md#Ribasim.discrete_control_condition-NTuple{4, Any})
- [`Ribasim.discrete_control_table`](index.md#Ribasim.discrete_control_table-Tuple{Ribasim.Model})
- [`Ribasim.expand_logic_mapping`](index.md#Ribasim.expand_logic_mapping-Tuple{Dict{Tuple{Int64, String}, String}})
- [`Ribasim.find_allocation_graph_edges!`](index.md#Ribasim.find_allocation_graph_edges!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Dict{Int64, Tuple{Int64, Symbol}}, Ribasim.Parameters, Vector{Int64}})
- [`Ribasim.findlastgroup`](index.md#Ribasim.findlastgroup-Tuple{Int64, AbstractVector{Int64}})
- [`Ribasim.findsorted`](index.md#Ribasim.findsorted-Tuple{Any, Any})
- [`Ribasim.flow_table`](index.md#Ribasim.flow_table-Tuple{Ribasim.Model})
- [`Ribasim.formulate_basins!`](index.md#Ribasim.formulate_basins!-Tuple{AbstractVector, Ribasim.Basin, AbstractMatrix, AbstractVector})
- [`Ribasim.formulate_flow!`](index.md#Ribasim.formulate_flow!-Tuple{Ribasim.ManningResistance, Ribasim.Parameters, AbstractVector, Float64})
- [`Ribasim.formulate_flow!`](index.md#Ribasim.formulate_flow!-Tuple{Ribasim.TabulatedRatingCurve, Ribasim.Parameters, AbstractVector, Float64})
- [`Ribasim.formulate_flow!`](index.md#Ribasim.formulate_flow!-Tuple{Ribasim.LinearResistance, Ribasim.Parameters, AbstractVector, Float64})
- [`Ribasim.get_area_and_level`](index.md#Ribasim.get_area_and_level-Tuple{Ribasim.Basin, Int64, Real})
- [`Ribasim.get_compressor`](index.md#Ribasim.get_compressor-Tuple{Ribasim.config.Results})
- [`Ribasim.get_fractional_flow_connected_basins`](index.md#Ribasim.get_fractional_flow_connected_basins-Tuple{Int64, Ribasim.Basin, Ribasim.FractionalFlow, Graphs.SimpleGraphs.SimpleDiGraph{Int64}})
- [`Ribasim.get_jac_prototype`](index.md#Ribasim.get_jac_prototype-Tuple{Ribasim.Parameters})
- [`Ribasim.get_level`](index.md#Ribasim.get_level-Tuple{Ribasim.Parameters, Int64, Float64})
- [`Ribasim.get_node_id_mapping`](index.md#Ribasim.get_node_id_mapping-Tuple{Ribasim.Parameters, Vector{Int64}, Vector{Int64}})
- [`Ribasim.get_node_in_out_edges`](index.md#Ribasim.get_node_in_out_edges-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}})
- [`Ribasim.get_scalar_interpolation`](index.md#Ribasim.get_scalar_interpolation-Tuple{Dates.DateTime, Float64, AbstractVector, Int64, Symbol})
- [`Ribasim.get_storage_from_level`](index.md#Ribasim.get_storage_from_level-Tuple{Ribasim.Basin, Int64, Float64})
- [`Ribasim.get_storages_and_levels`](index.md#Ribasim.get_storages_and_levels-Tuple{Ribasim.Model})
- [`Ribasim.get_storages_from_levels`](index.md#Ribasim.get_storages_from_levels-Tuple{Ribasim.Basin, Vector})
- [`Ribasim.get_tstops`](index.md#Ribasim.get_tstops-Tuple{Any, Dates.DateTime})
- [`Ribasim.get_value`](index.md#Ribasim.get_value-Tuple{Ribasim.Parameters, Int64, String, Float64, AbstractVector{Float64}, Float64})
- [`Ribasim.id_index`](index.md#Ribasim.id_index-Tuple{Dictionaries.Indices{Int64}, Int64})
- [`Ribasim.input_path`](index.md#Ribasim.input_path-Tuple{Ribasim.config.Config, String})
- [`Ribasim.is_flow_constraining`](index.md#Ribasim.is_flow_constraining-Tuple{Ribasim.AbstractParameterNode})
- [`Ribasim.is_flow_direction_constraining`](index.md#Ribasim.is_flow_direction_constraining-Tuple{Ribasim.AbstractParameterNode})
- [`Ribasim.load_data`](index.md#Ribasim.load_data-Tuple{SQLite.DB, Ribasim.config.Config, Type{<:Legolas.AbstractRecord}})
- [`Ribasim.load_structvector`](index.md#Ribasim.load_structvector-Union{Tuple{T}, Tuple{SQLite.DB, Ribasim.config.Config, Type{T}}} where T<:Tables.AbstractRow)
- [`Ribasim.nodefields`](index.md#Ribasim.nodefields-Tuple{Ribasim.Parameters})
- [`Ribasim.nodetype`](index.md#Ribasim.nodetype-Union{Tuple{Legolas.SchemaVersion{T, N}}, Tuple{N}, Tuple{T}} where {T, N})
- [`Ribasim.parse_static_and_time`](index.md#Ribasim.parse_static_and_time-Tuple{SQLite.DB, Ribasim.config.Config, String})
- [`Ribasim.path_exists_in_graph`](index.md#Ribasim.path_exists_in_graph-Tuple{Graphs.SimpleGraphs.SimpleDiGraph, Int64, Int64})
- [`Ribasim.process_allocation_graph_edges!`](index.md#Ribasim.process_allocation_graph_edges!-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, SparseArrays.SparseMatrixCSC{Float64, Int64}, Vector{Vector{Int64}}, Dict{Int64, Tuple{Int64, Symbol}}, Ribasim.Parameters})
- [`Ribasim.profile_storage`](index.md#Ribasim.profile_storage-Tuple{Vector, Vector})
- [`Ribasim.qh_interpolation`](index.md#Ribasim.qh_interpolation-Tuple{Int64, StructArrays.StructVector})
- [`Ribasim.reduction_factor`](index.md#Ribasim.reduction_factor-Union{Tuple{T}, Tuple{T, Real}} where T<:Real)
- [`Ribasim.results_path`](index.md#Ribasim.results_path-Tuple{Ribasim.config.Config, String})
- [`Ribasim.run`](index.md#Ribasim.run-Tuple{AbstractString})
- [`Ribasim.save_flow`](index.md#Ribasim.save_flow-Tuple{Any, Any, Any})
- [`Ribasim.scalar_interpolation_derivative`](index.md#Ribasim.scalar_interpolation_derivative-Tuple{DataInterpolations.LinearInterpolation{Vector{Float64}, Vector{Float64}, true, Float64}, Float64})
- [`Ribasim.seconds_since`](index.md#Ribasim.seconds_since-Tuple{Dates.DateTime, Dates.DateTime})
- [`Ribasim.set_current_value!`](index.md#Ribasim.set_current_value!-Tuple{NamedTuple, Vector{Int64}, StructArrays.StructVector, Dates.DateTime})
- [`Ribasim.set_model_state_in_allocation!`](index.md#Ribasim.set_model_state_in_allocation!-Tuple{Ribasim.AllocationModel, Ribasim.Parameters, Float64})
- [`Ribasim.set_static_value!`](index.md#Ribasim.set_static_value!-Tuple{NamedTuple, Vector{Int64}, StructArrays.StructVector})
- [`Ribasim.set_table_row!`](index.md#Ribasim.set_table_row!-Tuple{NamedTuple, Any, Int64})
- [`Ribasim.sorted_table!`](index.md#Ribasim.sorted_table!-Tuple{StructArrays.StructVector{<:Legolas.AbstractRecord}})
- [`Ribasim.timesteps`](index.md#Ribasim.timesteps-Tuple{Ribasim.Model})
- [`Ribasim.update_allocation!`](index.md#Ribasim.update_allocation!-Tuple{Any})
- [`Ribasim.update_basin`](index.md#Ribasim.update_basin-Tuple{Any})
- [`Ribasim.update_jac_prototype!`](index.md#Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Ribasim.AbstractParameterNode})
- [`Ribasim.update_jac_prototype!`](index.md#Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Union{Ribasim.LinearResistance, Ribasim.ManningResistance}})
- [`Ribasim.update_jac_prototype!`](index.md#Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Ribasim.PidControl})
- [`Ribasim.update_jac_prototype!`](index.md#Ribasim.update_jac_prototype!-Tuple{SparseArrays.SparseMatrixCSC{Float64, Int64}, Ribasim.Parameters, Union{Ribasim.User, Ribasim.Outlet, Ribasim.Pump, Ribasim.TabulatedRatingCurve}})
- [`Ribasim.update_tabulated_rating_curve!`](index.md#Ribasim.update_tabulated_rating_curve!-Tuple{Any})
- [`Ribasim.valid_discrete_control`](index.md#Ribasim.valid_discrete_control-Tuple{Ribasim.Parameters, Ribasim.config.Config})
- [`Ribasim.valid_edge_types`](index.md#Ribasim.valid_edge_types-Tuple{SQLite.DB})
- [`Ribasim.valid_edges`](index.md#Ribasim.valid_edges-Tuple{Dictionaries.Dictionary{Tuple{Int64, Int64}, Int64}, Dictionaries.Dictionary{Int64, Tuple{Symbol, Symbol}}})
- [`Ribasim.valid_flow_rates`](index.md#Ribasim.valid_flow_rates-Tuple{Vector{Int64}, Vector, Dict{Tuple{Int64, String}, NamedTuple}, Symbol})
- [`Ribasim.valid_fractional_flow`](index.md#Ribasim.valid_fractional_flow-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Vector{Int64}, Vector{Float64}})
- [`Ribasim.valid_n_neighbors`](index.md#Ribasim.valid_n_neighbors-Tuple{Ribasim.Parameters})
- [`Ribasim.valid_profiles`](index.md#Ribasim.valid_profiles-Tuple{Dictionaries.Indices{Int64}, Vector{Vector{Float64}}, Vector{Vector{Float64}}})
- [`Ribasim.valid_sources`](index.md#Ribasim.valid_sources-Tuple{Graphs.SimpleGraphs.SimpleDiGraph{Int64}, Dict{Int64, Tuple{Int64, Symbol}}})
- [`Ribasim.water_balance!`](index.md#Ribasim.water_balance!-Tuple{ComponentArrays.ComponentVector, ComponentArrays.ComponentVector, Ribasim.Parameters, Float64})
- [`Ribasim.write_arrow`](index.md#Ribasim.write_arrow-Tuple{AbstractString, NamedTuple, TranscodingStreams.Codec})
- [`Ribasim.config.@addfields`](index.md#Ribasim.config.@addfields-Tuple{Expr, Any})
- [`Ribasim.config.@addnodetypes`](index.md#Ribasim.config.@addnodetypes-Tuple{Expr})

