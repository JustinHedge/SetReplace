## WolframModelPlot

[Edge Type](#edge-type) | [GraphHighlight and GraphHighlightStyle](#graphhighlight-and-graphhighlightstyle) | ["HyperedgeRendering"](#hyperedgerendering) | [VertexCoordinateRules](#vertexcoordinaterules) | [VertexLabels](#vertexlabels) | [VertexSize and "ArrowheadLength"](#vertexsize-and-arrowheadlength) | ["MaxImageSize"](#maximagesize) | [Style Options](#style-options) | [Graphics Options](#graphics-options)

**`WolframModelPlot`** (aka `HypergraphPlot`) is a function used to visualize [`WolframModel`](WolframModel.md#wolframmodel-and-wolframmodelevolutionobject) states. It treats lists of vertices as ordered hypergraphs, and displays each hyperedge as a polygon with arrows showing the ordering:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}}]
```

<img src="DocsImages/WolframModelPlot.png" width="478">

Edges of any arity can be mixed. The binary edges are displayed as non-filled arrows, and the unary edges are shown as circles around the vertices:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4}, {4, 3}, {4, 5,
   6}, {1}, {6}, {6}}]
```

<img src="DocsImages/BinaryAndUnaryEdgesPlot.png" width="478">

Self-loops are shown as convex polygons around the appropriate number of circular arrows:

```wl
In[] := WolframModelPlot[{{1, 1, 1}, {1, 2, 3}, {3, 4, 4}}]
```

<img src="DocsImages/SelfLoopsPlot.png" width="478">

Note the difference between a hyper-self-loop and two binary edges pointing in opposite directions:

```wl
In[] := WolframModelPlot[{{1, 2, 1}, {2, 3}, {3, 2}}]
```

<img src="DocsImages/HyperSelfLoopDoubleBinaryEdgesComparison.png" width="478">

Multiedges are shown in a darker color (because of overlayed partially transparent polygons), or as separate polygons depending on the layout (and are admittedly sometimes hard to understand):

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {3, 4, 5}, {1, 6, 6}, {1, 6,
   6}}]
```

<img src="DocsImages/MultiedgesPlot.png" width="478">

`WolframModelPlot` is listable. Multiple hypergraphs can be plotted at the same time:

```wl
In[] := WolframModelPlot[{{{1, 2, 3}},
  {{1, 2, 3}, {3, 4, 5}},
  {{1, 2, 3}, {3, 4, 5}, {5, 6, 7}}}]
```

<img src="DocsImages/MultiplePlots.png" width="698">

Many [`WolframModel`](WolframModel.md#wolframmodel-and-wolframmodelevolutionobject) properties, such as [`"FinalStatePlot"`](Properties.md#plots-of-states) and [`"EventStatesPlotsList"`](Properties.md#plots-of-events), use `WolframModelPlot` to produce output. They accept the same set of options, as enumerated below.

### Edge Type

By default, `WolframModelPlot` assumes the hypergraph edges are ordered. It is also possible to treat edges as cyclic instead (i.e., assume [`RotateLeft`](https://reference.wolfram.com/language/ref/RotateLeft.html) and [`RotateRight`](https://reference.wolfram.com/language/ref/RotateRight.html) don't change the edge), in which case `"Cyclic"` should be used as the second argument to `WolframModelPlot`:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}}, "Cyclic"]
```

<img src="DocsImages/CyclicPlot.png" width="478">

### GraphHighlight and GraphHighlightStyle

Vertices and edges can be highlighted with the **`GraphHighlight`** option:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}, {7, 8, 2}, {4,
   9}, {9}}, GraphHighlight -> {{1, 2, 3}, 4, {9}}]
```

<img src="DocsImages/PlotWithHighlight.png" width="478">

For a hypergraph with multiedges, only the specified number of edges will be highlighted:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {1, 2, 3}, {3, 4}, {3, 4}, {3,
   4}, {4}, {4}}, GraphHighlight -> {{1, 2, 3}, {3, 4}, {3, 4}, {4}}]
```

<img src="DocsImages/PlotWithMultiedgeHighlight.png" width="478">

The style of the highlight can be specified with **`GraphHighlightStyle`**:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}, {7, 8, 2}, {4,
   9}, {9}}, GraphHighlight -> {{1, 2, 3}, 4, {9}},
 GraphHighlightStyle -> Directive[Darker @ Green, Thick]]
```

<img src="DocsImages/PlotWithGreenHighlight.png" width="478">

### "HyperedgeRendering"

By default, `WolframModelPlot` represents each hyperedge as a polygon. It is possible instead to drop the polygons (and the vertex layout adjustments that come with them), and simply split each hyperedge into a collection of binary edges by setting **`"HyperedgeRendering"`** to `"Subgraphs"`. This loses information (`{{1, 2}, {2, 3}}` and `{{1, 2, 3}}` would look the same), but might be useful if one does not care to see the separation between hyperedges:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}, {7, 8, 2}, {4,
   9}, {9}}, "HyperedgeRendering" -> "Subgraphs",
 VertexLabels -> Automatic]
```

<img src="DocsImages/SubgraphsHyperedgeRendering.png" width="478">

### VertexCoordinateRules

It is possible to manually specify some or all coordinates for the vertices:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {2, 4, 5}, {2, 6, 7, 8}, {8, 9, 1}},
 VertexCoordinateRules -> {1 -> {0, 0}, 2 -> {1, 0}, 3 -> {0, 1}},
 Axes -> True]
```

<img src="DocsImages/PlotWithCustomCoordinates.png" width="478">

Unfortunately, due to limitations of [`GraphEmbedding`](https://reference.wolfram.com/language/ref/GraphEmbedding.html), specifying coordinates of two or more vertices breaks the scaling of distances. As a result, vertices and arrowheads might appear too small or too large and need to be manually adjusted. This might also affect [`RulePlot`](#RulePlotOfWolframModel.md) in some cases.

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {2, 4, 5}, {2, 6, 7, 8}, {8, 9, 1}},
 VertexCoordinateRules -> {1 -> {0, 0}, 2 -> {1, 0}}]
```

<img src="DocsImages/IncorrectlyScaledPlot.png" width="466">

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {2, 4, 5}, {2, 6, 7, 8}, {8, 9, 1}},
 VertexCoordinateRules -> {1 -> {0, 0}, 2 -> {1, 0}},
 VertexSize -> 0.03, "ArrowheadLength" -> 0.06]
```

<img src="DocsImages/PlotWithCompensatedScale.png" width="448">

### VertexLabels

`"VertexLabels" -> Automatic` displays labels for vertices, similar to [`GraphPlot`](https://reference.wolfram.com/language/ref/GraphPlot.html):

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {2, 4, 5}, {2, 6, 7, 8}, {8, 9, 1}},
 VertexLabels -> Automatic]
```

<img src="DocsImages/PlotWithVertexLabels.png" width="478">

### VertexSize and "ArrowheadLength"

The size of vertices and the length of arrowheads (in the internal graphics units), can be adjusted with **`VertexSize`** and **`"ArrowheadLength"`** options respectively:

```wl
In[] := WolframModelPlot[{{1, 2, 3, 4}, {1, 5, 6}, {2, 7, 8}, {4, 6, 9}},
 VertexSize -> 0.1, "ArrowheadLength" -> 0.3]
```

<img src="DocsImages/PlotWithCustomElementSizes.png" width="478">

Note that unlike [`GraphPlot`](https://reference.wolfram.com/language/ref/GraphPlot.html), both vertices and arrowheads have a fixed size relative to the layout (in fact, the arrowheads are drawn manually as polygons). This fixed size implies that they scale proportionally when the image is resized, and do not overlay/disappear for tiny/huge graphs or image sizes.

These options can also be used to get rid of vertices and arrowheads altogether:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7}, {7, 8, 9}, {9, 10,
   1}}, "Cyclic", "ArrowheadLength" -> 0, VertexSize -> 0,
 VertexStyle -> Transparent]
```

<img src="DocsImages/PlotWithNoArrowsAndVertices.png" width="478">

As a neat example, one can even draw unordered hypergraphs:

```wl
In[] := WolframModelPlot[{{1, 2, 2}, {2, 3, 3}, {3, 1, 1}},
 "ArrowheadLength" -> 0, EdgeStyle -> <|{_, _, _ ..} -> Transparent|>,
  "EdgePolygonStyle" -> <|{_, _, _ ..} ->
    Directive[Hue[0.63, 0.66, 0.81], Opacity[0.1],
     EdgeForm[Directive[Hue[0.63, 0.7, 0.5], Opacity[0.7]]]]|>]
```

<img src="DocsImages/UnorderedPlot.png" width="478">

### "MaxImageSize"

**`"MaxImageSize"`** allows one to specify the image size while allowing for automatic reduction for very small hypergraphs. To demonstrate that, consider the difference:

```wl
In[] := WolframModelPlot[{{{1}}, {{1, 1}}, {{1, 2, 3}}},
 "MaxImageSize" -> 100]
```

<img src="DocsImages/PlotWithMaxImageSize.png" width="254">

```wl
In[] := WolframModelPlot[{{{1}}, {{1, 1}}, {{1, 2, 3}}}, ImageSize -> 100]
```

<img src="DocsImages/PlotWithImageSize.png" width="457">

### Style Options

There are four styling options: `PlotStyle`, `VertexStyle`, `EdgeStyle` and `"EdgePolygonStyle"`.

**`PlotStyle`** controls the overall style for everything, `VertexStyle` and `EdgeStyle` inherit from it:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}, {7, 8, 2}, {4,
   9}, {9}}, PlotStyle -> Directive[Blue, Dotted]]
```

<img src="DocsImages/PlotWithCustomPlotStyle.png" width="478">

**`VertexStyle`** works similar to [`GraphPlot`](https://reference.wolfram.com/language/ref/GraphPlot.html):

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}, {7, 8, 2}, {4,
   9}, {9}}, PlotStyle -> Directive[Blue, Dotted], VertexStyle -> Red]
```

<img src="DocsImages/PlotWithCustomVertexStyle.png" width="478">

**`EdgeStyle`** controls edge lines, and `"EdgePolygonStyle"` inherits from it (automatically adding transparency):

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}, {7, 8, 2}, {4,
   9}, {9}}, PlotStyle -> Directive[Blue, Dotted], VertexStyle -> Red,
  EdgeStyle -> Darker @ Green]
```

<img src="DocsImages/PlotWithCustomEdgeStyle.png" width="478">

Finally, **`"EdgePolygonStyle"`** controls the hyperedge polygons:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}, {7, 8, 2}, {4,
   9}, {9}}, PlotStyle -> Directive[Blue, Dotted], VertexStyle -> Red,
  EdgeStyle -> Darker @ Green,
 "EdgePolygonStyle" ->
  Directive[Lighter[Green, 0.9], EdgeForm[Dotted]]]
```

<img src="DocsImages/PlotWithCustomEdgePolygonStyle.png" width="478">

It is possible to specify styles separately for each edge and vertex. Vertex styles are specified in the same order as `Union @* Catenate` evaluated on the list of edges:

```wl
In[] := WolframModelPlot[{{1, 2, 3}, {3, 4, 5}, {5, 6, 7, 1}, {7, 8, 2}, {4,
   9}, {9}}, EdgeStyle -> ColorData[97] /@ Range[6],
 VertexStyle -> ColorData[98] /@ Range[9]]
```

<img src="DocsImages/PlotWithElementwiseStyles.png" width="478">

Alternatively, one can specify different styles for different patterns of elements. In this case, styles are specified as [`Association`](https://reference.wolfram.com/language/ref/Association.html)s with patterns for keys. This can be used to, for example, differently color edges of different arities:

```wl
In[] := WolframModelPlot[WolframModel[{{1, 2, 3}, {4, 5, 6}, {2, 5}, {5, 2}} ->
   {{7, 1, 8}, {9, 3, 10}, {11, 4, 12}, {13, 6, 14}, {7, 13}, {13,
     7}, {8, 10}, {10, 8}, {9, 11}, {11, 9}, {12, 14}, {14, 12}},
  {{1, 2, 3}, {4, 5, 6}, {1, 4}, {4, 1}, {2, 5}, {5, 2}, {3, 6}, {6,
    3}}, 6, "FinalState"],
 EdgeStyle -> <|{_, _} -> Darker @ Green, {_, _, _} -> Darker @ Red|>]
```

<img src="DocsImages/PlotWithAritywiseStyles.png" width="478">

### Graphics Options

All [`Graphics`](https://reference.wolfram.com/language/ref/Graphics.html) options are supported as well, such as [`Background`](https://reference.wolfram.com/language/ref/Background.html), [`PlotRange`](https://reference.wolfram.com/language/ref/PlotRange.html), [`Axes`](https://reference.wolfram.com/language/ref/Axes.html), etc.:

```wl
In[] := WolframModelPlot[WolframModel[{{1, 2}} -> {{1, 3}, {1, 3}, {3, 2}},
  {{1, 2}, {2, 3}, {3, 1}}, 7, "FinalState"], Background -> Black,
 PlotStyle -> White, GridLines -> Automatic,
 PlotRange -> {{30, 50}, {20, 40}}, Axes -> True]
```

<img src="DocsImages/PlotOfHypergraphFragment.png" width="478">