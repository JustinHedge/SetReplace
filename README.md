[![Discord](https://img.shields.io/discord/761616685173309481?logo=Discord)](https://discord.setreplace.org)

[Wolfram Models as Set Substitution Systems](#wolfram-models-as-set-substitution-systems) | [Getting Started](#getting-started) | [Symbols and Functions](#symbols-and-functions) | [Physics](#physics) | [Acknowledgements](#acknowledgements)

# Wolfram Models as Set Substitution Systems

## Set Substitution Systems

*SetReplace* is a [Wolfram Language](https://www.wolfram.com/language/) package for manipulating set substitution systems. To understand what a set substitution system does consider an unordered set of elements:

```wl
{1, 2, 5, 3, 6}
```

We can set up an operation on this set which would take any of the two elements and replace them with their sum:

```wl
{a_, b_} :> {a + b}
```

In *SetReplace*, this can be expressed as the following (the new element `1 + 2 -> 3` is put at the end)

```wl
In[] := SetReplace[{1, 2, 5, 3, 6}, {a_, b_} :> {a + b}]
Out[] = {5, 3, 6, 3}
```

Note that this is similar to [`SubsetReplace`](https://reference.wolfram.com/language/ref/SubsetReplace.html) function of Wolfram Language (introduced in version 12.1, it replaces all non-overlapping subsets at once by default):

```wl
In[] := SubsetReplace[{1, 2, 5, 3, 6}, {a_, b_} :> a + b]
Out[] = {3, 8, 6}
```

## Wolfram Models

A more interesting case (which we call a Wolfram model) is one where the set elements are related to each other. Specifically, we can consider a set of ordered lists of atomic vertices; in other words, an ordered hypergraph.

As an example consider a set:

```wl
{{1, 2, 3}, {2, 4, 5}, {4, 6, 7}}
```

We can render it as a collection of ordered hyperedges:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {2, 4, 5}, {4, 6, 7}},
 VertexLabels -> Automatic]
```

<img src="Documentation/Images/BasicHypergraphPlot.png" width="478">

We can then have a rule which would pick a subset of these hyperedges related through common vertices (much like a join query) and replace them with something else:

```wl
{{v1_, v2_, v3_}, {v2_, v4_, v5_}} :>
 Module[{v6}, {{v5, v6, v1}, {v6, v4, v2}, {v4, v5, v3}}]
```

Note the [`Module`](https://reference.wolfram.com/language/ref/Module.html) on the right-hand side creates a new variable (vertex) which causes the hypergraph to grow. Due to optimizations, it's not always a [`Module`](https://reference.wolfram.com/language/ref/Module.html) that creates vertices, so its name may be different. After a single replacement we get this (the new vertex is v11):

```wl
In[] := WolframModelPlot[SetReplace[{{1, 2, 3}, {2, 4, 5}, {4, 6, 7}},
  {{v1_, v2_, v3_}, {v2_, v4_, v5_}} :>
   Module[{v6}, {{v5, v6, v1}, {v6, v4, v2}, {v4, v5, v3}}]],
 VertexLabels -> Automatic]
```

<img src="Documentation/Images/EvolutionResult1Step.png" width="478">

After 10 steps, we get a more complicated structure:

```wl
In[] := WolframModelPlot[SetReplace[{{1, 2, 3}, {2, 4, 5}, {4, 6, 7}},
  {{v1_, v2_, v3_}, {v2_, v4_, v5_}} :>
   Module[{v6}, {{v5, v6, v1}, {v6, v4, v2}, {v4, v5, v3}}], 10],
 VertexLabels -> Automatic]
```

<img src="Documentation/Images/EvolutionResult10Steps.png" width="478">

And after 100 steps, it gets even more elaborate:

```wl
In[] := WolframModelPlot[SetReplace[{{1, 2, 3}, {2, 4, 5}, {4, 6, 7}},
  {{v1_, v2_, v3_}, {v2_, v4_, v5_}} :>
   Module[{v6}, {{v5, v6, v1}, {v6, v4, v2}, {v4, v5, v3}}], 100]]
```

<img src="Documentation/Images/EvolutionResult100Steps.png" width="478">

Exploring the hypergraph models of this variety is the primary purpose of this package.

# Getting Started

## Dependencies

You only need three things to use *SetReplace*:

* Windows, macOS 10.12+, or Linux.
* [Wolfram Language 12.1+](https://www.wolfram.com/language/) including [WolframScript](https://www.wolfram.com/wolframscript/). A free version is available as [Wolfram Engine](https://www.wolfram.com/engine/).
* A C++17 compiler to build the low-level part of the package. Instructions on how to set up a compiler to use in WolframScript are [here](https://reference.wolfram.com/language/CCompilerDriver/tutorial/SpecificCompilers.html#509267359).

## Build Instructions

To build:

1. `cd` to the root directory of the repository.
2. Run `./build.wls` to create the paclet file. If you see an error message about c++17, make sure the C++ compiler you are using is up-to-date. If your default system compiler does not support c++17, you can choose a different one with environmental variables. The following, for instance, typically works on a Mac:

    ```bash
    COMPILER=CCompilerDriver\`ClangCompiler\`ClangCompiler COMPILER_INSTALLATION=/usr/bin ./build.wls
    ```

    Here `ClangCompiler` can be replaced with one of ``<< CCompilerDriver`; "Compiler" /. CCompilerDriver`CCompilers[Full]``, and `COMPILER_INSTALLATION` is a directory in which the compiler binary can be found.

3. Run `./install.wls` to install the paclet into your Wolfram system.
4. Evaluate `PacletDataRebuild[]` in all running Wolfram kernels.
5. Evaluate ``<< SetReplace` `` every time before using the package.

A less frequently updated version is available through the Wolfram public paclet server and can be installed with `PacletInstall["SetReplace"]`.

## Contributing

Keep in mind that this is an active research project. While we try to keep the main functionality backward compatible, it might change in the future as we adjust our models and find better ways of analysis. Keep that in mind when building on top of *SetReplace*, and keep track of [git SHAs](Documentation/SymbolsAndFunctions/UtilityFunctions/BuildData.md) as you go.

*SetReplace* is an open-source project, and everyone is welcome to contribute. Read our [contributing guidelines](.github/CONTRIBUTING.md) to get started.

We have a [Discord server](https://discord.setreplace.org). If you would like to contribute but have questions or don't know where to start, this is the perfect place! In addition to helping new contributors, we discuss feature and research ideas. So, if you are interested, please join!

# Symbols and Functions

- [SetReplace\*](Documentation/SymbolsAndFunctions/SetReplace*.md)
- [ToPatternRules](Documentation/SymbolsAndFunctions/ToPatternRules.md)
- Utility Functions
  - [BuildData](Documentation/SymbolsAndFunctions/UtilityFunctions/BuildData.md)
  - [Generalized Grid Graph](Documentation/SymbolsAndFunctions/UtilityFunctions/GeneralizedGridGraph.md)
  - [Hypergraph Automorphism Group](Documentation/SymbolsAndFunctions/UtilityFunctions/HypergraphAutomorphismGroup.md)
  - [Hypergraph Unifications](Documentation/SymbolsAndFunctions/UtilityFunctions/HypergraphUnifications.md)
  - [Subhypergraph](Documentation/SymbolsAndFunctions/UtilityFunctions/Subhypergraph.md)
  - [Wolfram Model Rule Value](Documentation/SymbolsAndFunctions/UtilityFunctions/WolframModelRuleValue.md)
  - [Wolfram Physics Project Style Data](Documentation/SymbolsAndFunctions/UtilityFunctions/WolframPhysicsProjectStyleData.md)
- [WolframModel and WolframModelEvolutionObject](Documentation/SymbolsAndFunctions/WolframModelAndWolframModelEvolutionObject/WolframModelAndWolframModelEvolutionObject.md)
  - Properties
    - ["AllEdgesThroughoutEvolution"](Properties/AllEdgesThroughoutEvolution.md)
    - [Causal Graphs](Properties/CausalGraphs.md)
    - [Creator And Destroyer Events](Properties/CreatorAndDestroyerEvents.md)
    - [Edge And Event Generations](Properties/EdgeAndEventGenerations.md)
    - [ElementCountLists](Properties/ElementCountLists.md)
    - [Event Counts](Properties/EventCounts.md)
    - [Events](Properties/Events.md)
    - [Events And States](Properties/EventsAndStates.md)
    - [Expression Separations](Properties/ExpressionSeparations.md)
    - [Final Element Counts](Properties/FinalElementCounts.md)
    - [Generation Counts](Properties/GenerationCounts.md)
    - [Plots Of Events](Properties/PlotsOfEvents.md)
    - [Plots Of States](Properties/PlotsOfStates.md)
    - [RuleIndices For Events](Properties/RuleIndicesForEvents.md)
    - [Rules](Properties/Rules.md) | [States](Properties/States.md)
    - [States As Edge Indices](Properties/StatesAsEdgeIndices.md)
    - [Termination Reason](Properties/TerminationReason.md)
    - [Total Element Counts](Properties/TotalElementCounts.md)
    - [Version](Properties/Version.md) 
  - Options
    - ["VertexNamingFunction"](Options/VertexNamingFunction)
    - ["IncludePartialGenerations"](Options/IncludePartialGenerations.md)
    - ["IncludeBoundaryEvents"](Options/IncludeBoundaryEvents.md)
    - ["EventOrderingFunction"](Options/EventOrderingFunction.md)
    - ["EventSelectionFunction"](Options/EventSelectionFunction.md)
    - ["EventDeduplication"](Options/EventDeduplication.md)
    - ["IncludeBoundaryEvents"](Options/IncludeBoundaryEvents.md)
    - ["IncludePartialGenerations"](Options/IncludePartialGenerations.md)
    - [Method](Options/Method.md)
    - [TimeConstraint](Options/TimeConstraint.md)
- [WolframModelPlot](Documentation/SymbolsAndFunctions/WolframModelPlot.md)
- [RulePlot of WolframModel](Documentation/SymbolsAndFunctions/RulePlotOfWolframModel.md)

# Physics

A hypothesis is that spacetime at small scales is a network, and the fundamental law of physics is a system similar to the one this package implements.

A slightly different version of this system was first introduced in *Stephen Wolfram*'s [A New Kind Of Science](https://www.wolframscience.com/nks/chap-9--fundamental-physics/).

You can find many more details about our physics results in *Stephen Wolfram*'s [Technical Introduction](https://www.wolframphysics.org/technical-introduction/), and *Jonathan Gorard*'s papers on [Relativity](https://www.wolframcloud.com/obj/wolframphysics/Documents/some-relativistic-and-gravitational-properties-of-the-wolfram-model.pdf) and [Quantum Mechanics](https://www.wolframcloud.com/obj/wolframphysics/Documents/some-quantum-mechanical-properties-of-the-wolfram-model.pdf). And there is much more on [wolframphysics.org](https://www.wolframphysics.org).

# Acknowledgements

In additional to commit authors and reviewers, *Stephen Wolfram* has contributed to the API design of most functions, and *Jeremy Davis* has contributed to the visual style of [`WolframModelPlot`](Documentation/SymbolsAndFunctions/WolframModelPlot.md), [`RulePlot`](Documentation/SymbolsAndFunctions/RulePlotOfWolframModel.md) and [`"CausalGraph"`](Documentation/SymbolsAndFunctions/WolframModelAndWolframModelEvolutionObject/Properties/CausalGraphs.md).