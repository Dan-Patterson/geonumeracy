point in polygon revisited.
--------------------------

The "norm" is points on the boundary of a polygon are considered "outside", but there are situations where you
may want to include them.  Determining whether a point is on or extremely close to the boundary is 
doeable but it requires checks.

Let us start with a polygon, oriented clockwise beginning at position [1., 1.]

```python
array([[   1.000,    1.000],
       [   1.000,    9.000],
       [   9.000,    9.000],
       [   9.000,    1.000],
       [   1.000,    1.000]])
```

The following figure shows an annotated list of points and there position relative to outside, on or inside the polygon boundary.
One of the problems is with points that are collinear to the segments/edges of the boundary.
A collinear point can indicate that a point is equal to one of the vertices of the polygon
 (points 5, 9, 13, 17),

```python
array([[   1.000,    1.000],
       [   1.000,    9.000],
       [   9.000,    9.000],
       [   9.000,    1.000]])
```

or it is on the line formed by two polygon vertices (points 21, 24, 27 and 30).

```python
array([[   1.000,    5.000],
       [   5.000,    9.000],
       [   9.000,    5.000],
       [   5.000,    1.000]])
```
       
Points that are inside can be obvious (7, 11, 15, 19)

```python
array([[   1.500,    1.500],
       [   1.500,    8.500],
       [   8.500,    8.500],
       [   8.500,    1.500]])
```

or less obvious like those that are 1 millimeter inside (20, 23, 26 and 29)

```python
array([[   1.001,    4.000],
       [   4.000,    8.999],
       [   8.999,    6.000],
       [   6.000,    1.001]])
```

Just a check for collinearity isn't enough because a point can be external to the polygon boundary but it is 
definitely outside the boundary and not on it.  This case is represented by points 4 and 6.  Point 21 however is
on the boundary, but you could tell visually ehh? between 20, 21 and 22?

A "distance" check can be added to the toolset.  This entails determining a candidate point's distance between two polygon
vertices.  The point's distance to the "from" vertex plus the distance to the "to" vertex will equal
the segment/edge length if the point is collinear and on the boundary.

Too much effort? Not if you want to definitely include or exclude points that may be on the polygon boundary.

Now some of the checks
for points 0, 4, 5, 6, 7

segments are all the same length.  point 0 is on the start vertex of the polygon.  It ascends upwards and is oriented
clockwise ending at the same point.

seg_len = array([  64.000,   64.000,   64.000,   64.000])  # -- the edge lengths

Now if we focus on the aforementioned points we can use the following definitions:

  - A point is collinear to an edge if the absolute value of the crossproduct is equal to zero
   (or some small value accounting for floating point issues.

    is_collinear = np.abs(diff_) < 1e-10

  - A point is on a line segment if the dot product is greater than or equal zero
    and less than or equal to the segment length.

  - A point is on a edge if it is equal to either of the two edge vertices or somewhere in between.

    is_within_segment = (dot_ >= 0) & (dot_ <= seg_len)

Combining these ideas yields:

on_edge = np.any(is_collinear & is_within_segment, axis=1)

For the points in question, the collinearity check yields:

diff_[[0, 4, 5, 6, 7]]

```python
  edges  0         1         2         3          points
array([[   8.000,  -72.000,  -72.000,    8.000],  0
       [   4.000,  -64.000,  -68.000,    0.000],  4
       [   0.000,  -64.000,  -64.000,    0.000],  5
       [  -0.000,  -68.000,  -64.000,    4.000],  6
       [  -4.000,  -60.000,  -60.000,   -4.000]]) 7
```

is_collinear[[0, 4, 5, 6, 7]]

```python
  edges 0  1  2  3    points 
array([[0, 0, 0, 0],    0    not collinear
       [0, 0, 0, 1],    4    collinear to the last edge (3)
       [1, 0, 0, 1],    5    collinear to the first and last edge (0 and 3) (it is equal to a polygon vertex)
       [1, 0, 0, 0],    6    collinear to the first edge (0)
       [0, 0, 0, 0]])   7    not collinear
```

Now for the dot product results for the points in question.  The output is annotated, edges are the columns.
Points are the rows.

dot_[[0, 4, 5, 6, 7]]

```python
  edges   0         1         2         3          points  
array([[  -8.000,   -8.000,   72.000,   72.000],   0
       [   0.000,   -4.000,   64.000,   68.000],   4
       [   0.000,    0.000,   64.000,   64.000],   5
       [  -4.000,    0.000,   68.000,   64.000],   6
       [   4.000,    4.000,   60.000,   60.000]])  7
```

Which are within the segments using the following:

is_within_segment = (dot_ >= 0) & (dot_ <= seg_len)

is_within_segment[[0, 4, 5, 6, 7]]

```python
  edges 0  1  2  3     points  
array([[0, 0, 0, 0],   0
       [1, 0, 1, 0],   4
       [1, 1, 1, 1],   5
       [0, 1, 0, 1],   6
       [1, 1, 1, 1]])  7
```
       
remembering :
on_edge = np.any(is_collinear & is_within_segment, axis=1)

on_edge[[0, 4, 5, 6, 7]]
array([0, 0, 1, 0, 0])  # -- that is, point 5
