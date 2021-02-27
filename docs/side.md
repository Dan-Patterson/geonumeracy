## Side

<img src="../images/side.png" align="right" width="400"/>

---
Which side?  Whose side?  Left side? Right side?

The points? Both? Just one? Which one?

The segment?

To answer these questions observe the red and green point markers numbered in clockwise order for each of the two polygons.

Our brain can quickly decide which side IF we know the ``sidedness rule``.  

If the segments are ordered clockwise, then any point will be *inside* if it is to the *right*.
If the polygon that we are testing as the home for the points is convex, then an inside point
will be to the right of all the line segments.

This is the principle behind the *winding number* test for point inclusion.  It, however, accounts
for idiosyncracies that are associated with concave polygons.

**Looking at sides**

The ``_side_`` code listed below, returns four values.

- r       : the side array
- inside  : the points inside (right of) a segment
- outside : the points outside (left of) a segment
- equal_  : any points that may intersect (equal to) one another by virtue of their equality or potential intersection on the segment.

```python

# check E for inclusion in c0
r, _, _, _ = _side_(E, c0)
# inside, right-side
r_lt0 = (r < 0).all(-1) * -1  # array([ 0,  0, -1, -1, -1,  0, -1, -1,  0,  0,  0,  0,  0])
w_lt0 = np.nonzero(r_lt0)[0]  # array([2, 3, 4, 6, 7], dtype=int64)
# outside, left-side
r_gt0 = (r > 0).any(-1) * 1   # array([1, 1, 0, 0, 0, 1, 0, 0, 1, 1, 1, 1, 1])
w_gt0 = np.nonzero(r_gt0)[0]  # array([ 0,  1,  5,  8,  9, 10, 11, 12], dtype=int64)
# equal,  none

# check c0 for inclusion in E
r0, _, _, _ = _side_(c0, E)
r0_lt0 = (r0 < 0).all(-1) * -1  # array([0, 0, 0, 0, 0])
r0_gt0 = (r0 > 0).any(-1) * 1   # array([1, 1, 1, 1, 1])
r0_eq0 = (r0 == 0).any(-1) * 1  # array([1, 0, 0, 1, 1])

Green    Red
pnts     segments
0       [1, 1, 1, 0] 
1       [0, 1, 1, 1]
2       [1, 1, 1, 1]  inside
3       [1, 1, 1, 1]  inside
4       [1, 1, 1, 1]  inside
5       [1, 1, 1, 0]
6       [1, 1, 1, 1]  inside
7       [1, 1, 1, 1]  inside
8       [1, 1, 1, 0]
9       [1, 1, 1, 0]
10      [1, 1, 0, 1]
11      [1, 1, 0, 1]
12      [1, 1, 1, 0]
```       

```
(r0 < 0) * -1
array([[ 0, -1, -1,  0, -1,  0, -1, -1, -1,  0, -1, -1],
       [-1,  0,  0, -1,  0,  0,  0, -1,  0,  0,  0, -1],
       [-1, -1,  0,  0,  0, -1,  0, -1,  0,  0,  0, -1],
       [-1, -1, -1,  0,  0, -1,  0,  0,  0, -1, -1,  0],
       [ 0, -1, -1,  0, -1,  0, -1, -1, -1,  0, -1, -1]])

(r0 > 0) * 1
array([[0, 0, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0],
       [0, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 0],
       [0, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0],
       [0, 0, 0, 1, 1, 0, 1, 1, 1, 0, 0, 0],
       [0, 0, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0]])

(r0 == 0) * 1
Out[40]: 
array([[1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
       [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
       [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
       [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1],
       [1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]])
```
