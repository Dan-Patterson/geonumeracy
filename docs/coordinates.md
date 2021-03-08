# Coordinates and NumPy

NumPy ndarrays can be used to represent simple 2D and 3D geometry objects.

The square ``A`` is simply constructed from 2D points ordered in a clockwise fashion with the first and last point being equal.
That geometry also represents a polyline, the polygons's perimeter.

```python
In [1]: A  # -- a square, oriented clockwise
Out[1]:
array([[  0.00,   0.00],
       [  0.00,  10.00],
       [ 10.00,  10.00],
       [ 10.00,   0.00],
       [  0.00,   0.00]])
```

We can translate it (move it) by adding/subtracting values from both the X and Y values.
```python
In [2]:A + [2., 2.]
Out[2]:
array([[  2.00,   2.00],
       [  2.00,  12.00],
       [ 12.00,  12.00],
       [ 12.00,   2.00],
       [  2.00,   2.00]])
```
Do simple math, like the average including all points and excluding certain points like the duplicate start/end point.

```python
In [3]: np.mean(A, axis=0)
Out[3]: array([  4.00,   4.00])

In [4]: np.mean(A[:-1], axis=0)
Out[4]: array([  5.00,   5.00])
```
The ``[:-1]`` in [4] represents `slicing`.  The interpretation, then reads, `use all values up to, but not including the last` (-1, counting back from the end.).
It could have also have been written as ``A[:4]`` meaning include all points up to the 4th index.

Slicing takes a simple format.  *array[start index: stop index: step]*.  You need to remember that arrays use a 0-based count, so index value 1, is the second entry.
The following shows how to slice *A* starting at index position 1, up to index 5, sampling every 2nd value
```python
In [5]: A[1:5:2]
Out[5]: 
array([[  0.00,  10.00],
       [ 10.00,   0.00]])
```

## Creating geometry

A number of standard geometric patterns can be created readily using numpy.  The basic code and examples follow.

### Rectangles

The pattern for a sequence of rectanges requires:
  - a increment for both the x and y directions (dx and dy)
  - an origin for the bottom left corner
  - the number of rows and columns to create

For example
```
In [1]: rectangle(dx=1, dy=-1, x_cols=2, y_rows=2, orig_x=0, orig_y=1)
Out[1]: 
array([[[   0.0,    0.0],
        [   0.0,    1.0],
        [   1.0,    1.0],
        [   1.0,    0.0],
        [   0.0,    0.0]],

       [[   1.0,    0.0],
        [   1.0,    1.0],
        [   2.0,    1.0],
        [   2.0,    0.0],
        [   1.0,    0.0]],

       [[   0.0,    1.0],
        [   0.0,    2.0],
        [   1.0,    2.0],
        [   1.0,    1.0],
        [   0.0,    1.0]],

       [[   1.0,    1.0],
        [   1.0,    2.0],
        [   2.0,    2.0],
        [   2.0,    1.0],
        [   1.0,    1.0]]])
```

The function code with a full documentation string follows.

```
def rectangle(dx=1, dy=-1, x_cols=1, y_rows=1, orig_x=0, orig_y=1):
    """Create a point array to represent a series of rectangles or squares.

    Parameters
    ----------
    dx, dy : number
        x direction increment, +ve moves west to east, left/right.
        y direction increment, -ve moves north to south, top/bottom.
    x_cols, y_rows : integers
        The number of columns and rows to produce.
    orig_x, orig_y : number
        Planar coordinates assumed.  You can alter the location of the origin
        by specifying the correct combination of (dx, dy) and (orig_x, orig_y).
        The defaults produce a clockwise, closed-loop geometry, beginning and
        ending in the upper left.

    Example
    -------
    Stating the obvious... squares form when dx == dy.

    X = [0.0, 0.0, dx, dx, 0.0] # X, Y values for a unit square
    Y = [0.0, dy, dy, 0.0, 0.0]

    Cells are constructed clockwise from the bottom-left.  The rectangular grid
    is constructed from the top-left.  Specifying an origin (upper left) of
    (0, 2) yields a bottom-right corner of (3,0) when the following are used.

    >>> z = rectangle(dx=1, dy=1, x_cols=3, y_rows=2, orig_x=0, orig_y=2)

    The first `cell` will be in the top-left and the last `cell` in the
    bottom-right.
    """
    seed = np.array([[0.0, 0.0], [0.0, dy], [dx, dy], [dx, 0.0], [0.0, 0.0]])
    a = [seed + [j * dx, i * dy]      # make the shapes
         for i in range(0, y_rows)      # cycle through the rows
         for j in range(0, x_cols)]     # cycle through the columns
    a = np.asarray(a) + [orig_x, orig_y-dy]
    return a
```

### Triangles


The pattern for a sequence of triangles requires:
  - a increment for both the x and y directions (dx and dy)
  - the number of rows and columns to create

```
# Create a series of triangles with 3 columns and 2 rows, with X, Y steps of 1x1.  The lower left is at (0, 0).
In [1]: a = triangle(dx=1, dy=1, x_cols=3, y_rows=2, orig_x=0, orig_y=1)
In [2]: a
Out[2]: 
array([[[  0.000,   0.000],
        [  0.500,   1.000],
        [  1.000,   0.000],
        [  0.000,   0.000]],

       [[  0.500,   1.000],
        [  1.500,   1.000],
        [  1.000,   0.000],
        [  0.500,   1.000]],
        ... snip ...
In [3]: a.shape
Out[3]: (12, 4, 2)
```


A `seed` shape consists of two triangles, one pointing up and one down.  This is the basic building block which is repeated.

```
def triangle(dx=1, dy=1, x_cols=1, y_rows=1, orig_x=0, orig_y=1):
    """Create a row of meshed triangles.

    The triangles are essentially bisected squares and not equalateral.
    The triangles per row will not be terminated in half triangles to
    `square off` the area of coverage.  This is to ensure that all geometries
    have the same area and point construction.

    Parameters
    ----------
    See `rectangles` for shared parameter explanation.
    """
    a, dx, b = dx/2.0, dx, dx*1.5
    # X, Y values for a unit triangle, point up and point down
    seedU = np.array([[0.0, 0.0], [a, dy], [dx, 0.0], [0.0, 0.0]])
    seedD = np.array([[a, dy], [b, dy], [dx, 0.0], [a, dy]])
    seed = np.array([seedU, seedD])
    a = [seed + [j * dx, i * dy]       # make the shapes
         for i in range(0, y_rows)       # cycle through the rows
         for j in range(0, x_cols)]      # cycle through the columns
    a = np.asarray(a)
    s1, s2, s3, s4 = a.shape
    a = a.reshape(s1 * s2, s3, s4)
    return a

