---
title: A* Search
tags: graph, algorithm, traversal, array
expertise: advanced
firstSeen: 2022-08-03T05:30:00-05:00
---

Searches for a path in a graph using the A* Search algorithm.

- Function (helper) ```isValid``` checks if the node is within the bounds of the graph.
- Function (helper) ```isUnblocked``` checks if the node is an obstacle.
- Function (helper) ```isDestination``` checks if the node is the destination.
- Function (helper) ```calculateDistance``` computes the distance in a straight line between two nodes.
- Function (helper) ```tracePath``` prints the path once the algorithm finds it.
- Function (helper) ```updateSuccessor``` checks if destination was found, if it wasn't update the node's ```g```, ```h```, and ```f``` according to the algorithm.
- Initialize ```cellDetails```, and ```closedList```, and ```openList``` arrays.
- ```cellDetails``` contains the information of each node.
- ```closedList``` contains the nodes that have been visited.
- ```openList``` contains the nodes to visit next.
- Iterate over each node adjacent to the one we are currently.

```js
const isValid = (row, col) => {
  return row >= 0 && row < ROWS && col >= 0 && col < COLS;
};

const isUnblocked = (grid, row, col) => {
  return grid[row][col] === 1 ? true : false;
};

const isDestination = (row, col, dest) => {
  return row === dest[0] && col == dest[1] ? true : false;
};

const calculateDistance = (row, col, dest) => {
  return Math.sqrt(Math.pow(row - dest[0], 2) + Math.pow(col - dest[1], 2));
};

const tracePath = (cellDetails, dest) => {
  console.log("The path is:");
  let row = dest[0];
  let col = dest[1];
  console.log(row, col);
  let path = [];

  while (
    !(
      cellDetails[row][col].parentI === row &&
      cellDetails[row][col].parentJ === col
    )
  ) {
    path.push([row, col]);
    let tempRow = cellDetails[row][col].parentI;
    let tempCol = cellDetails[row][col].parentJ;
    row = tempRow;
    col = tempCol;
  }

  path.push([row, col]);
  path.reverse();
  console.log(path);
};

const updateSuccessor = (
  grid,
  cellDetails,
  closedList,
  openList,
  parentI,
  parentJ,
  i,
  j,
  dest,
  foundDest = false
) => {
  let gNew = 0;
  let fNew = 0;
  let hNew = 0;

  if (isValid(i, j) && !foundDest) {
    if (isDestination(i, j, dest)) {
      cellDetails[i][j].parentI = parentI;
      cellDetails[i][j].parentJ = parentJ;

      console.log("[SUCCESS] destination found");

      tracePath(cellDetails, dest);
      foundDest = true;

      return foundDest;
    } else if (!closedList[i][j] && isUnblocked(grid, i, j)) {
      gNew = cellDetails[parentI][parentJ].g + 1.0;
      hNew = calculateDistance(i, j, dest);
      fNew = gNew + hNew;

      if (cellDetails[i][j].f === Infinity || cellDetails[i][j].f > fNew) {
        openList.push([fNew, [i, j]]);
        openList.sort((a, b) => b < a);

        cellDetails[i][j] = {
          parentI: parentI,
          parentJ: parentJ,
          f: fNew,
          g: gNew,
          h: hNew,
        };
      }
    }
  }
  return foundDest;
};

const aStarSearch = (grid, src, dest) => {
  if (!isValid(src[0], src[1])) {
    console.log("[ERROR] not a valid source");
    return;
  }

  if (!isValid(dest[0], dest[1])) {
    console.log("[ERROR] not a valid destination");
    return;
  }

  if (!isUnblocked(grid, src[0], src[1])) {
    console.log("[ERROR] source is blocked");
    return;
  }

  if (!isUnblocked(grid, dest[0], dest[1])) {
    console.log("[ERROR] destination is blocked");
    return;
  }

  const closedList = new Array(ROWS).fill(0).map(() => new Array(COLS).fill(0));
  const cellDetails = new Array(ROWS).fill(0).map(() =>
    new Array(COLS).fill({
      parentI: -1,
      parentJ: -1,
      f: Infinity,
      g: Infinity,
      h: Infinity,
    })
  );

  let [i, j] = [src[0], src[1]];

  cellDetails[i][j] = {
    parentI: i,
    parentJ: j,
    f: 0.0,
    g: 0.0,
    h: 0.0,
  };

  const openList = [];
  openList.push([0.0, [i, j]]);

  let foundDest = false;

  while (!(openList.length === 0)) {
    let p = openList.shift();
    [i, j] = [p[1][0], p[1][1]];
    closedList[i][j] = true;

    // North      Successor
    foundDest = updateSuccessor( grid, cellDetails, closedList, openList, i, j, i - 1, j, dest, foundDest );

    // North-East Successor
    foundDest = updateSuccessor( grid, cellDetails, closedList, openList, i, j, i - 1, j + 1, dest, foundDest );

    // East       Successor
    foundDest = updateSuccessor( grid, cellDetails, closedList, openList, i, j, i, j + 1, dest, foundDest );

    // South-East Successor
    foundDest = updateSuccessor( grid, cellDetails, closedList, openList, i, j, i + 1, j + 1, dest, foundDest );

    // South      Successor
    foundDest = updateSuccessor( grid, cellDetails, closedList, openList, i, j, i + 1, j, dest, foundDest );

    // South-West Successor
    foundDest = updateSuccessor( grid, cellDetails, closedList, openList, i, j, i + 1, j - 1, dest, foundDest );

    // West       Successor
    foundDest = updateSuccessor( grid, cellDetails, closedList, openList, i, j, i, j - 1, dest, foundDest );

    // North-West Successor
    foundDest = updateSuccessor( grid, cellDetails, closedList, openList, i, j, i - 1, j - 1, dest, foundDest );
  }

  if (!foundDest) {
    console.log("[INFO] destination not found");
  }

  return;
};
```

Example:
```js
const grid = [
  [1, 0, 1, 1, 1, 1, 0, 1, 1, 1],
  [1, 1, 1, 0, 1, 1, 1, 0, 1, 1],
  [1, 1, 1, 0, 1, 1, 1, 0, 1, 1],
  [0, 0, 1, 0, 1, 0, 0, 0, 0, 1],
  [1, 1, 1, 0, 1, 1, 1, 0, 1, 0],
  [0, 0, 1, 1, 1, 1, 0, 1, 0, 0],
  [1, 0, 0, 0, 0, 1, 0, 0, 0, 1],
  [1, 0, 1, 1, 1, 1, 0, 1, 1, 1],
  [1, 1, 1, 0, 0, 0, 1, 0, 0, 1],
];

const src = [8, 9];
const dest = [0, 0];
const ROWS = grid.length;
const COLS = grid[0].length;

aStarSearch(grid, src, dest);
```
Output Path:
```
[8,9]->[7,8]->[7,7]->[8,6]->[7,5]->[6,5]->
[5,4]->[5,3]->[4,2]->[3,2]->[2,1]->[1,0]->[0,0]
```