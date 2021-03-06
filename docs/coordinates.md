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

### Triangles

```
# Create a series of triangles with 3 columns and 2 rows, with X, Y steps of 1x1.  The lower left is at (0, 0).
In [5]: a = triangle(dx=1, dy=1, x_cols=3, y_rows=2, orig_x=0, orig_y=1, kind=2, asGeo=False)
in [6]: a
Out[6]: 
array([[[  0.000,   0.000],
        [  0.500,   1.000],
        [  1.000,   0.000],
        [  0.000,   0.000]],

       [[  0.500,   1.000],
        [  1.500,   1.000],
        [  1.000,   0.000],
        [  0.500,   1.000]],
        ... snip ...
In [7]: a.shape
Out[7]: (12, 4, 2)
```
