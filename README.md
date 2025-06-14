# LoupekineColourings

This program is part of the master thesis "Normal Edge-Colourings of Cubic Graphs" by Sam Cornelis, under supervision of Dr. D. Mattiolo and Prof. J. Goedgebeur, which is part of the Master in Mathematics at KULeuven. The program allows the user to check whether a Loupekine snark constructed with a specified block admits a normal 5-edge-colouring, by constructing a set of colourings of pairs and triples of this block which is needed for the proof in the thesis.

### Usage
The only input of the program is located in the first line below `if __name__ == '__main__'`. Here you specify the graph from which the block is created, the path you want to remove, and whether the block has to be crossed. For the graph you can use one the predefined graphs at the top of the code, or you can add your own graph.

While running the code it will print the following information in the order below:
- The amount of colourings of a single block
- The amount of $B$-colourings after removing duplicate colourings
- The amount of colourings on a pair of blocks
- The amount of $B^2$-colourings after removing duplicate colourings
- The amount of colourings on a triple of blocks
- The amount of $B^3$-colourings after removing duplicate colourings
- A progress meter while it creates the graph of all colourings
- A progress meter while it reduces the set until it obeys conditions (5) and (6) from the thesis

After the program has finished it will print the following results in the order below:
- The total amount of colourings in the final set
- The amount of $B^2$-colourings in the final set
- The amount of $B^3$-colourings in the final set
- The list of spoke configurations for which no self-attaching colouring exists
- The list of pairs of spoke configurations for which no two colourings attach with those colours on their spokes

The main information to determine succes are the last two points. The first will determine if the final set also obeys conditions (3) and (4) from the thesis. From the second one we can determine if triples with middle edges coloured 1 have to be excluded. If this last list is empty, no triples have to be excluded. If however there are no edges between colourings with middle spokes coloured 1, these are not part of the set, and the extra condition will be necessary. Note that in the code the colours start with 0, so instead we will see that triples with middle edge coloured 0 are not included in any edge.

### Examples
If we run the program on the crossed Petersen block, which we do by inputting `B = createBlock(petersenGraph, [2,0,3], True)`, we get the following output:

```
colourings in collection: 648
P2-Colourings: 444
P3-Colourings: 204
self adjacent not present: []
edges not present: []
```

From the last two lines we determine no triples have to be exluded, and we retrieve the result of Theorem 6.2.1 in the thesis.

If we run the programm on the normal Petersen block by inputting `B = createBlock(petersenGraph, [2,0,3], False)`, we get the following output:

```
colourings in collection: 560
P2-Colourings: 444
P3-Colourings: 116
self adjacent not present: ['102', '201']
edges not present: ['102-102', '102-201', '201-201']
```

We see from the last line that there are no edges connecting triples with middle spoke coloured 1, and thus have to exclude them. This thus gives us the main result of the thesis, Theorem 5.1.1

If lastly we run the program on the block created from the first Blanusa snark by removing the path $(1,10,7)$ by inputting `B = createBlock(firstBlanusaSnark, [0,9,6], False)` (note the vertices are numbered starting with 0 in the program, so we have to shift everything down), we get the following output:

```
colourings in collection: 671
P2-Colourings: 365
P3-Colourings: 306
self adjacent not present: ['012', '021', '102', '120', '201', '210']
edges not present: ['012-012', '012-021', '012-102', '012-120', '012-201', '012-210', '021-021', '021-102', '021-120', '021-201', '021-210', '102-102', '102-120', '102-201', '102-210', '120-120', '120-201', '120-210', '201-201', '201-210', '210-210']
```

We see that the resulting set does not have any attaching triples, and we thus cannot get any result for this graph. This result is indeed the same as in Table 6.1 in the thesis.
