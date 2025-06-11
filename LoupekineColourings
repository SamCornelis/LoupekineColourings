from bisect import bisect_left
import copy

# graphs are represented by a dictionary where "vertices" is range(n) with n the amount of vertices,
# and "edges" is a list with the edges, which are sets of two vertices.
# below are some predefined graphs, but you can add graphs if needed.
petersenGraph = {"vertices": range(10),
                 "edges": [{0,1}, {0,2}, {0,3}, {1,6}, {1,7}, {2,4}, {2,8}, {3,5}, {3,9}, {4,5}, {4,7}, {5,6}, {6,8},
                           {7,9}, {8,9}]}

firstBlanusaSnark = {"vertices": range(18),
                     "edges": [{0,1}, {0,2}, {0,9}, {1,3}, {1,8}, {2,4}, {2,7}, {3,5}, {3,14}, {4,6}, {4,15}, {5,7},
                               {5,9}, {6,8}, {6,9}, {7,10}, {8,11}, {10,12}, {10,17}, {11,13}, {11,16}, {12,13},
                               {12,14}, {13,15}, {14,16}, {15,17}, {16,17}]}

secondBlanusaSnark = {"vertices": range(18),
                      "edges": [{0,1}, {0,2}, {0,7}, {1,4}, {1,10}, {2,11}, {2,13}, {3,5}, {3,6}, {3,14}, {4,12},
                                {4,15}, {5,8}, {5,13}, {6,9}, {6,15}, {7,8}, {7,16}, {8,11}, {9,10}, {9,12}, {10,17},
                                {11,14}, {12,14}, {13,16}, {15,17}, {16,17}]}

flowerSnarkJ5 = {"vertices": range(20),
                 "edges": [{0,1}, {0,2}, {0,7}, {1,3}, {1,11}, {2,4}, {2,10}, {3,5}, {3,18}, {4,6}, {4,19}, {5,8},
                           {5,10}, {6,9}, {6,11}, {7,8}, {7,9}, {8,12}, {9,13}, {10,14}, {11,15}, {12,13}, {12,16},
                           {13,17}, {14,15}, {14,16}, {15,17}, {16,18}, {17,19}, {18,19}]}

# Input:
# G = a graph
# path = a list of 3 vertices in G which form a path and which are not in a 3 or 4 cycle
# crossed = whether the block has crossed connections
# Output: a graph which is the block created by removing the path from G
def createBlock(G, path, crossed=False):
    G = copy.deepcopy(G)
    adjacency = [[],[],[]]
    for e in G["edges"]:
        for i in range(3):
            if path[i] in e:
                for w in e:
                    if not w in path:
                        adjacency[i].append(w)

    for i in adjacency:
        i.sort()
    if crossed:
        adjacency[2].reverse()

    verticesAmount = len(G["vertices"])

    G["edges"].append({adjacency[0][0], verticesAmount})
    G["edges"].append({adjacency[0][1], verticesAmount+1})
    G["edges"].append({adjacency[2][0], verticesAmount+2})
    G["edges"].append({adjacency[2][1], verticesAmount+3})

    for e in G["edges"]:
        if adjacency[1][0] in e:
            G["edges"].remove(e)
            G["edges"].insert(0, e)

    G["edges"].insert(0, {adjacency[1][0], verticesAmount+4})

    i = 0
    while i < len(G["edges"]):
        if G["edges"][i].intersection(set(path)):
            del G["edges"][i]
        else:
            newEdge = set()
            for v in G["edges"][i]:
                change = 0
                for w in path:
                    if v > w:
                        change +=1
                newEdge.add(v-change)
            G["edges"][i] = newEdge
            i += 1

    G["vertices"] = range(len(G["vertices"]) + 2)

    return G


# Input:
# colourAmount: amount of colours
# colours: a partial normal colouring of a graph
# edgeAdjacency: a list containing the amount of adjacent coloured edges for each edge
# Output: a list with all normal colourings with the specified amount of colours containing the initial colouring
def createEdgeColourings(colourAmount, colours, edgeAdjacency):
    colourings = []
    edgeToColour = {}
    maxAdjacency = -1

    for e in edgeAdjacency:
        if e[1] > maxAdjacency:
            maxAdjacency = e[1]
            edgeToColour = e[0]

    if edgeToColour == {}:
        return [colours]

    newEdgeAdjacency = copyColouring(edgeAdjacency)
    for e in newEdgeAdjacency:
        if e[0] == edgeToColour: e[1] = -1
        else:
            for v in edgeToColour:
                if v in e[0]:
                    if e[1] != -1: e[1] += 1

    for i in range(colourAmount):
        newColours = copyColouring(colours)
        for e in newColours:
            if e[0] == edgeToColour:
                e[1] = i

        if isNormalColouredEdge(newColours, edgeToColour):
            colourings.extend(createEdgeColourings(colourAmount, newColours, newEdgeAdjacency))

    return colourings

# Input: a colouring
# Output: a copy of the colouring
def copyColouring(colouring):
    colouringCopy = []
    for e in colouring:
        colouringCopy.append([e[0].copy(),e[1]])

    return colouringCopy

# Input: a colouring and an edge of the coloured graph
# Output: whether the colouring is a proper colouring at the ends of the edge and normal at the edge
def isNormalColouredEdge(colours, edge):
    for v in edge:
        if not isColouredVertex(colours, v):
            return False

        for e in colours:
            if v in e[0] and e[0] != edge:
                if not isNormalEdge(colours, e[0]):
                    return False

    if not isNormalEdge(colours, edge):
        return False

    return True

# Input: a colouring and a vertex of the coloured graph
# Output: whether the edges incident with the vertex have different colours
def isColouredVertex(colours, vertex):
    incidentColours = []
    for e in colours:
        if e[1] != -1 and vertex in e[0]: incidentColours.append(e[1])

    if len(incidentColours) != len(set(incidentColours)):
        return False

    return True

# Input: a colouring and an edge of the coloured graph
# Output: whether the edge is normal in the colouring
def isNormalEdge(colours, edge):
    incidentColours = []
    for v in edge:
        for e in colours:
            if e[1] != -1 and v in e[0]:
                incidentColours.append(e[1])

    if len(incidentColours) == 6:
        if len(set(incidentColours)) == 3 or len(set(incidentColours)) == 5:
            return True
        else:
            return False
    else:
        return True

# Input: a block B
# Output: all normal 5-edge-colourings of B that obey condition (1) and (2) in the thesis
def createBColourings(B):
    colouringPreset = []
    for e in B["edges"]:
        colouringPreset.append([e, -1])

    edgeAdjacencyPreset = copyColouring(colouringPreset)

    colouringPreset[0][1] = 0
    colouringPreset[1][1] = 1
    colouringPreset[2][1] = 2

    for e in edgeAdjacencyPreset:
        e[1] = 0
        for f in colouringPreset:
            if f[0] == e[0] and f[1] != -1:
                e[1] = -1
                break
            else:
                for v in e[0]:
                    if v in f[0] and f[1] != -1:
                        e[1] += 1

    B12Colourings = createEdgeColourings(5, colouringPreset, edgeAdjacencyPreset)

    allB0Colourings = []
    for c in B12Colourings:
        allB0Colourings.append(c)
        allB0Colourings.append(permute2Colours(c, 1, 2))
        allB0Colourings.append(permute2Colours(permute2Colours(c, 1, 3), 2, 4))
        allB0Colourings.append(permute2Colours(permute2Colours(c, 1, 4), 2, 3))

    allB1Colourings = []
    allB2Colourings = []
    for c in allB0Colourings:
        allB1Colourings.append(permute2Colours(c, 0, 1))
        allB2Colourings.append(permute2Colours(c, 0, 2))

    return allB0Colourings, allB1Colourings, allB2Colourings

# Input: a colouring and two colour of the colouring
# Output: a copy of the colouring where the two colours are swapped
def permute2Colours(colouring, c1, c2):
    newColouring = copyColouring(colouring)

    for e in newColouring:
        if e[1] == c1:
            e[1] = c2
        elif e[1] == c2:
            e[1] = c1

    return newColouring

# Input: a list of colourings of the same graph and a list of edges of that graph
# Output: a list containing the configurations of each of the colourings. A configuration is a list which contains a list with two elements for each edge.
#         the first element is the colour of the edge, the second is a list of all colours of edges adjacent to the edge
def createConfigurations(colourings, edges):
    configurations = []
    for colouring in colourings:
        configuration = []
        for e in edges:
            edgeColour = -1
            for c in colouring:
                if e == c[0]: edgeColour = c[1]

            incidentColours = []
            for v in e:
                for c in colouring:
                    if v in c[0] and c[1] != edgeColour: incidentColours.append(c[1])

            configuration.append([edgeColour, incidentColours])

        configurations.append(configuration)

    return configurations

# Input: a list of configurations of colourings of a graph
# Output: a directed graph whose vertices are the colourings corresponding to the configurations,
#         where a colouring has an edge to another colouring if their combined colouring is normal
#         the list of edges of this graph will be ordered
def createColouringsGraph(configurations, progressMeter = False):
    lenConfigurations = len(configurations)
    vertices = list(range(lenConfigurations))
    edges = []

    for v in vertices:
        if progressMeter and (v + 1) % round(lenConfigurations / 100) == 0:
            print("\r" + str(round((v + 1) / lenConfigurations * 100)) + "%", end="", flush=True)
        for w in vertices:
            config1 = configurations[v]
            config2 = configurations[w]

            if config1[-2][0] == config2[-4][0] and config1[-1][0] == config2[-3][0]:
                if not len(set(config1[-2][1]).intersection(config2[-4][1])) == 1:
                    if not len(set(config1[-1][1]).intersection(config2[-3][1])) == 1:
                        edges.append([v,w])

    G = {"vertices": vertices, "edges": edges}
    return G

# Input: the directed graph of all colourings of B with spoke coloured 0, and a list of all these colourings
# Output: a list containing all colourings of B^2 that obey condition (2) in the thesis
def createB2Colourings(B0ColouringsGraph, B0Colourings):
    B20Colourings = []
    for e in B0ColouringsGraph["edges"]:
        B20Colourings.append(combine2BColourings(B0Colourings[e[0]], B0Colourings[e[1]]))

    B2Colourings = []
    for c in B20Colourings:
        B2Colourings.append(c)
        B2Colourings.append(permute2Colours(c, 0, 1))
        B2Colourings.append(permute2Colours(c, 0, 2))

    return B2Colourings

# Input: two colourings of B
# Output: their combined colouring on B^2
def combine2BColourings(c1, c2):
    leftColouring = []
    rightColouring = []

    i = 0
    while i < BInternalEdges:
        e = c1[i+1][0]
        leftColouring.append(c1[i+1])
        rightColouring.append([{min(e) + BInternalVertices, max(e) + BInternalVertices}, c2[i+1][1]])
        i += 1

    combinedColouring = leftColouring + rightColouring

    #colouring the connecting edges
    combinedColouring.append([{min(c1[-2][0]), min(c2[-4][0]) + BInternalVertices}, c1[-2][1]])
    combinedColouring.append([{min(c1[-1][0]), min(c2[-3][0]) + BInternalVertices}, c1[-1][1]])

    #colouring the horizontal semiedges
    combinedColouring.append([{min(c1[-4][0]), 2*BInternalVertices}, c1[-4][1]])
    combinedColouring.append([{min(c1[-3][0]), 2*BInternalVertices + 1}, c1[-3][1]])
    combinedColouring.append([{min(c2[-2][0]) + BInternalVertices, 2*BInternalVertices + 2}, c2[-2][1]])
    combinedColouring.append([{min(c2[-1][0]) + BInternalVertices, 2*BInternalVertices + 3}, c2[-1][1]])

    #colouring the vertical semiedges
    combinedColouring.insert(0, [{min(c2[0][0]) + BInternalVertices, 2 * BInternalVertices + 5}, c2[0][1]])
    combinedColouring.insert(0, [{min(c1[0][0]), 2*BInternalVertices + 4}, c1[0][1]])

    return combinedColouring

# Input: the directed graph of all colourings of B, and a list of all these colourings
# Output: a list containing all colourings of B^3 that obey condition (1) in the thesis
def createB3Colourings(BColouringsGraph, BColourings):
    BColouringsAdjacency = createAdjacency(BColouringsGraph)

    B3012Colourings = []
    for v in BColouringsGraph["vertices"]:
        if BColourings[v][0][1] == 0:
            for w in BColouringsAdjacency[v]:
                if BColourings[w][0][1] == 1:
                    for u in BColouringsAdjacency[w]:
                        if BColourings[u][0][1] == 2:
                            B3012Colourings.append(combine3BColourings(BColourings[v], BColourings[w], BColourings[u]))

    B3Colourings = []
    for c in B3012Colourings:
        B3Colourings.append(c)
        B3Colourings.append(permute2Colours(c, 1, 2))
        B3Colourings.append(permute2Colours(c, 0, 1))
        B3Colourings.append(permute2Colours(permute2Colours(c, 1, 2), 0, 1))
        B3Colourings.append(permute2Colours(permute2Colours(c, 1, 2), 0, 2))
        B3Colourings.append(permute2Colours(c, 0, 2))

    return B3Colourings

# Input: a directed graph G whose list of edges is ordered
# Output: a list which for each vertex of G contains the ordered list of vertices it is adjacent to.
def createAdjacency(G):
    adjacency = []
    for v in G["vertices"]:
        adjacency.append([])

    for e in G["edges"]:
        adjacency[e[0]].append(e[1])

    return adjacency

# Input: three colourings of B
# Output: their combined colouring on B^3
def combine3BColourings(c1, c2, c3):
    leftColouring = []
    middleColouring = []
    rightColouring = []

    i = 0
    while i < BInternalEdges:
        e = c1[i+1][0]
        leftColouring.append(c1[i+1])
        middleColouring.append([{min(e) + BInternalVertices, max(e) + BInternalVertices}, c2[i+1][1]])
        rightColouring.append([{min(e) + 2*BInternalVertices, max(e) + 2*BInternalVertices}, c3[i+1][1]])
        i += 1

    combinedColouring = leftColouring + middleColouring + rightColouring

    #colouring the connecting edges
    combinedColouring.append([{min(c1[-2][0]), min(c2[-4][0]) + BInternalVertices}, c1[-2][1]])
    combinedColouring.append([{min(c1[-1][0]), min(c2[-3][0]) + BInternalVertices}, c1[-1][1]])
    combinedColouring.append([{min(c2[-2][0]) + BInternalVertices, min(c3[-4][0]) + 2*BInternalVertices}, c2[-2][1]])
    combinedColouring.append([{min(c2[-1][0]) + BInternalVertices, min(c3[-3][0]) + 2*BInternalVertices}, c2[-1][1]])

    #colouring the horizontal semiedges
    combinedColouring.append([{min(c1[-4][0]), 3*BInternalVertices}, c1[-4][1]])
    combinedColouring.append([{min(c1[-3][0]), 3*BInternalVertices + 1}, c1[-3][1]])
    combinedColouring.append([{min(c3[-2][0]) + 2*BInternalVertices, 3*BInternalVertices + 2}, c3[-2][1]])
    combinedColouring.append([{min(c3[-1][0]) + 2*BInternalVertices, 3*BInternalVertices + 3}, c3[-1][1]])

    # colouring the vertical semiedges
    combinedColouring.insert(0, [{min(c3[0][0]) + 2*BInternalVertices, 3*BInternalVertices + 6}, c3[0][1]])
    combinedColouring.insert(0, [{min(c2[0][0]) + BInternalVertices, 3*BInternalVertices + 5}, c2[0][1]])
    combinedColouring.insert(0, [{min(c1[0][0]), 3*BInternalVertices + 4}, c1[0][1]])

    return combinedColouring

# Input: a list of colourings of the same graph and the list of their configurations
# Output: a copy of the list of colourings and configurations where colourings with a duplicate configuration are removed
def cullColourings(colourings, configurations):
    culledColourings = []
    for c in colourings:
        culledColourings.append(copyColouring(c))

    culledConfigurations = copyConfigurations(configurations)

    i = 0
    while i < len(culledColourings):
        j = i+1
        while j < len(culledColourings):
            if culledConfigurations[i] == culledConfigurations[j]:
                    culledColourings.pop(j)
                    culledConfigurations.pop(j)
            else:
                j += 1
        i += 1

    return culledColourings, culledConfigurations

# Input: a list of configurations of colourings of a graph
# Output: a copy of that list of configurations
def copyConfigurations(configurations):
    configurationsCopy = []
    for c in configurations:
        cCopy = []
        for e in c:
            eCopy = [e[0],[]]
            for v in e[1]:
                eCopy[1].append(v)
            cCopy.append(eCopy)
        configurationsCopy.append(cCopy)

    return configurationsCopy

# Input: a directed graph of B^2- and B^3-colourings
# Output: a subgraph of this graph such that the colourings of its remaining vertices obey conditions (5) and (6) from the thesis
def createTriangleGraph(colouringsGraph):
    newVertices = colouringsGraph["vertices"].copy()
    adjacency = createAdjacency(colouringsGraph)
    lenAdjacency = len(adjacency)

    allGood = False
    loopNumber = 0
    while not allGood:
        loopNumber += 1
        allGood = True
        for i in range(lenAdjacency):
            if (i+1) % round(lenAdjacency/100) == 0:
                print("\rloop " + str(loopNumber) + ": " + str(round((i+1)/lenAdjacency*100)) + "%", end="", flush=True)

            j = 0
            while j < len(adjacency[i]):
                v = adjacency[i][j]
                if not hasTriangles(adjacency, [i, v]):
                    allGood = False
                    if contains(adjacency[i], i):
                        vertexToRemove = v
                    else:
                        vertexToRemove = i

                    newVertices[vertexToRemove] = -1
                    adjacency[vertexToRemove].clear()
                    for w in adjacency:
                        if contains(w, vertexToRemove):
                            w.remove(vertexToRemove)
                else:
                    j += 1

    for v in newVertices:
        if v != -1 and adjacency[v] == []:
            newVertices[v] = -1

    newEdges = []
    for i in range(len(adjacency)):
        for v in adjacency[i]:
            newEdges.append([i, v])

    newGraph = {"vertices": newVertices, "edges": newEdges}
    return newGraph

# Input: the adjacency of a directed graph of of B^2- and B^3-colourings, and an edge in that graph
# Output: whether the colourings of this edge obey conditions (5) and (6) from the thesis
def hasTriangles(adjacency, edge):
    if len(configurations[edge[0]]) == 7 and len(configurations[edge[1]]) == 7 and configurations[edge[0]][1][0] == 0 and configurations[edge[1]][1][0] == 0:
        goodEdge = False
        triangles = {"012": [], "021": [], "102": [], "120": [], "201": [], "210": []}
        for v in adjacency[edge[0]]:
            if len(configurations[v]) == 7:
                if contains(adjacency[v], edge[1]):
                    triangles[str(configurations[v][0][0]) + str(configurations[v][1][0]) + str(configurations[v][2][0])].append(v)
                    goodEdge = True
                    for i in triangles:
                        if triangles[i] == []: goodEdge = False
                    if goodEdge: break

        if not goodEdge:
            for i in triangles:
                if triangles[i] == []: return False

    elif len(configurations[edge[0]]) == 7 and len(configurations[edge[1]]) == 7 and configurations[edge[0]][1][0] in {0, 1} and configurations[edge[1]][1][0] in {0, 1}:
        goodEdge = False
        triangles = {"012": [], "021": [], "120": [], "210": []}
        for v in adjacency[edge[0]]:
            if len(configurations[v]) == 7 and configurations[v][1][0] in {1, 2}:
                if contains(adjacency[v], edge[1]):
                    triangles[str(configurations[v][0][0]) + str(configurations[v][1][0]) + str(configurations[v][2][0])].append(v)
                    goodEdge = True
                    for i in triangles:
                        if triangles[i] == []: goodEdge = False
                    if goodEdge: break

        if not goodEdge:
            for i in triangles:
                if triangles[i] == []: return False

    elif len(configurations[edge[0]]) == 7 and len(configurations[edge[1]]) == 7:
        goodEdge = False
        triangles = {"021": [], "120": []}
        for v in adjacency[edge[0]]:
            if len(configurations[v]) == 7 and configurations[v][1][0] == 2:
                if contains(adjacency[v], edge[1]):
                    triangles[str(configurations[v][0][0]) + str(configurations[v][1][0]) + str(configurations[v][2][0])].append(v)
                    goodEdge = True
                    for i in triangles:
                        if triangles[i] == []: goodEdge = False
                    if goodEdge: break

        if not goodEdge:
            for i in triangles:
                if triangles[i] == []: return False

    goodEdge = False
    triangles = {"00": [], "11": [], "22": []}
    for v in adjacency[edge[0]]:
        if len(configurations[v]) == 6:
            if contains(adjacency[v], edge[1]):
                triangles[str(configurations[v][0][0]) + str(configurations[v][1][0])].append(v)
                goodEdge = True
                for i in triangles:
                    if triangles[i] == []: goodEdge = False
                if goodEdge: break

    if not goodEdge:
        for i in triangles:
            if triangles[i] == []: return False

    return True

# Input: a sorted list of integers and an integer x
# Output: whether this list contains x
def contains(list, x):
    i = bisect_left(list, x)
    return i != len(list) and list[i] == x



if __name__ == '__main__':
#   enter the block you want to test below by selecting a snark, a path of three vertices to remove, and whether the block crossed
#   if needed you can add a snark at the top to be able to make blocks from that snark
    B = createBlock(petersenGraph, [2, 0, 3], False)

    BInternalVertices = len(B["vertices"]) - 5
    BInternalEdges = len(B["edges"]) - 5

#   first we create all colourings of a single block
    print("creating B colourings...")
    B0Colourings, B1Colourings, B2Colourings = createBColourings(B)
    BColourings = B0Colourings + B1Colourings + B2Colourings
    print("B colourings: " + str(len(BColourings)))

#   we then create the list of configurations of these colourings
    B0Configurations = createConfigurations(B0Colourings, [B["edges"][0], B["edges"][-4], B["edges"][-3], B["edges"][-2], B["edges"][-1]])
    B1Configurations = createConfigurations(B1Colourings, [B["edges"][0], B["edges"][-4], B["edges"][-3], B["edges"][-2], B["edges"][-1]])
    B2Configurations = createConfigurations(B2Colourings, [B["edges"][0], B["edges"][-4], B["edges"][-3], B["edges"][-2], B["edges"][-1]])
    BConfigurations = B0Configurations + B1Configurations + B2Configurations

#   we remove colours with duplicated configurations
    BColourings, BConfigurations = cullColourings(BColourings, BConfigurations)
    print("after culling: " + str(len(BColourings)))

    B0Colourings, B0Configurations = cullColourings(B0Colourings, B0Configurations)

#   we then create all B2-colourings, create the list of their configurations, and remove duplicates
    B0ColouringsGraph = createColouringsGraph(B0Configurations)
    B2Colourings = createB2Colourings(B0ColouringsGraph, B0Colourings)
    print("B2 colourings: " + str(len(B2Colourings)))

    B2Configurations = createConfigurations(B2Colourings, [{min(B["edges"][0]), 2*BInternalVertices + 4},
                                                           {min(B["edges"][0]) + BInternalVertices, 2*BInternalVertices + 5},
                                                           {min(B["edges"][-4]), 2*BInternalVertices},
                                                           {min(B["edges"][-3]), 2*BInternalVertices + 1},
                                                           {min(B["edges"][-2]) + BInternalVertices, 2*BInternalVertices + 2},
                                                           {min(B["edges"][-1]) + BInternalVertices, 2*BInternalVertices + 3}])

    B2Colourings, B2Configurations = cullColourings(B2Colourings, B2Configurations)
    print("after culling: " + str(len(B2Colourings)))

#   we do the same for the B3-configurations
    BColouringsGraph = createColouringsGraph(BConfigurations)
    B3Colourings = createB3Colourings(BColouringsGraph, BColourings)
    print("B3 colourings: " + str(len(B3Colourings)))

    B3Configurations = createConfigurations(B3Colourings, [{min(B["edges"][0]), 3*BInternalVertices + 4},
                                                           {min(B["edges"][0]) + BInternalVertices, 3*BInternalVertices + 5},
                                                           {min(B["edges"][0]) + 2*BInternalVertices, 3*BInternalVertices + 6},
                                                           {min(B["edges"][-4]), 3*BInternalVertices},
                                                           {min(B["edges"][-3]), 3*BInternalVertices + 1},
                                                           {min(B["edges"][-2]) + 2*BInternalVertices, 3*BInternalVertices + 2},
                                                           {min(B["edges"][-1]) + 2*BInternalVertices, 3*BInternalVertices + 3}])

    B3Colourings, B3Configurations = cullColourings(B3Colourings, B3Configurations)
    print("after culling: " + str(len(B3Colourings)))

    configurations = B2Configurations + B3Configurations

#   we create the directed graph of all B2- and B3-colourings
    print("creating colouring graph...")
    colouringsGraph = createColouringsGraph(configurations, True)

#   we then reduce the collection until it obeys conditions (5) and (6) from the thesis
    print("\ncreating set of colourings...")
    triangleGraph = createTriangleGraph(colouringsGraph)
    triangleAdjacency = createAdjacency(triangleGraph)

#   we remove all colourings that are not adjacent to anything
    for v in triangleGraph["vertices"]:
        if v != -1:
            if triangleAdjacency[v] == []:
                triangleGraph["vertices"][v] = -1

#   we count the number of colourings, and we check if there are colourings that attach to themselves such that the collection obeys condition (3) and (4) from the thesis
    vertexAmount = 0
    P2Amount = 0
    P3Amount = 0
    selfAdjacentB2Colourings = {"00": False, "11": False, "22": False}
    selfAdjacentB3Colourings = {"012": False, "021": False, "102": False, "120": False, "201": False, "210": False}
    for v in triangleGraph["vertices"]:
        if v != -1:
            vertexAmount += 1
            if len(configurations[v]) == 6: P2Amount += 1
            else: P3Amount += 1

            if v in triangleAdjacency[v]:
                if len(configurations[v]) == 6:
                    selfAdjacentB2Colourings[str(configurations[v][0][0]) + str(configurations[v][1][0])] = True
                else:
                    selfAdjacentB3Colourings[str(configurations[v][0][0]) + str(configurations[v][1][0]) + str(configurations[v][2][0])] = True
    selfAdjacentNotPresent = []
    for i in selfAdjacentB2Colourings:
        if not selfAdjacentB2Colourings[i]: selfAdjacentNotPresent.append(i)
    for i in selfAdjacentB3Colourings:
        if not selfAdjacentB3Colourings[i]: selfAdjacentNotPresent.append(i)

#   we check which kind of connections are still present in the final collection. This tells us if we need to exlude triples with middle spoke coloured 1 or 2.
#   If for example there exist no connections including a triple with spokes coloured 213 or 312, it means these colouring were weeded out and we need to exclude triples with middle spoke coloured 1
    B2B2Edges = {"00-00": False, "00-11": False, "00-22": False, "11-11": False, "11-22": False, "22-22": False}
    B2B3Edges = {"00-012": False, "00-021": False, "00-102": False, "00-120": False, "00-201": False, "00-210": False,
                 "11-012": False, "11-021": False, "11-102": False, "11-120": False, "11-201": False, "11-210": False,
                 "22-012": False, "22-021": False, "22-102": False, "22-120": False, "22-201": False, "22-210": False,}
    B3B3Edges = {"012-012": False, "012-021": False, "012-102": False, "012-120": False, "012-201": False, "012-210": False,
                 "021-021": False, "021-102": False, "021-120": False, "021-201": False, "021-210": False,
                 "102-102": False, "102-120": False, "102-201": False, "102-210": False,
                 "120-120": False, "120-201": False, "120-210": False, "201-201": False, "201-210": False, "210-210": False,}
    for e in triangleGraph["edges"]:
        if len(configurations[e[0]]) == 6:
            if len(configurations[e[1]]) == 6:
                B2B2Edges[str(configurations[e[0]][0][0]) + str(configurations[e[0]][1][0]) + "-" + str(configurations[e[1]][0][0]) + str(configurations[e[1]][1][0])] = True
            else:
                B2B3Edges[str(configurations[e[0]][0][0]) + str(configurations[e[0]][1][0]) + "-" + str(configurations[e[1]][0][0]) + str(configurations[e[1]][1][0]) + str(configurations[e[1]][2][0])] = True
        else:
            if len(configurations[e[1]]) == 7:
                B3B3Edges[str(configurations[e[0]][0][0]) + str(configurations[e[0]][1][0]) + str(configurations[e[0]][2][0]) + "-" +
                          str(configurations[e[1]][0][0]) + str(configurations[e[1]][1][0]) + str(configurations[e[1]][2][0])] = True
    edgesNotPresent = []
    for i in B2B2Edges:
        if not B2B2Edges[i]: edgesNotPresent.append(i)
    for i in B2B3Edges:
        if not B2B3Edges[i]: edgesNotPresent.append(i)
    for i in B3B3Edges:
        if not B3B3Edges[i]: edgesNotPresent.append(i)

    print("\ncolourings in collection: " + str(vertexAmount))
    print("B2-Colourings: " + str(P2Amount))
    print("B3-Colourings: " + str(P3Amount))
    print("self adjacent not present: " + str(selfAdjacentNotPresent))
    print("edges not present: " + str(edgesNotPresent))
