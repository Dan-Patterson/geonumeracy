## Geometry as arrays ##

Take an array of 2D point objects.

```python
In [1]: sq
Out[1]: 
Geo([[   0.00,    0.00],
     [   2.00,    8.00],
     [   8.00,   10.00],
     [  10.00,   10.00],
     [  10.00,    8.00],
     [   9.00,    1.00],
     [   0.00,    0.00],
     [   3.00,    3.00],
     [   7.00,    3.00],
     [   5.00,    7.00],
     [   3.00,    3.00],
     [   8.00,   10.00],
     [   8.00,   11.00],
     [   8.00,   12.00],
     [  12.00,   12.00],
     [  12.00,    8.00],
     [  10.00,    8.00],
     [  10.00,   10.00],
     [   8.00,   10.00],
     [   5.00,   10.00],
     [   5.00,   12.00],
     [   6.00,   12.00],
     [   8.00,   12.00],
     [   8.00,   11.00],
     [   5.00,   10.00],
     [   5.00,   12.00],
     [   5.00,   15.00],
     [   7.00,   14.00],
     [   6.00,   12.00],
     [   5.00,   12.00]])
```
In this case, the array represents a **Geo** array, an array that represents polygon or polyline geometry.  The details are covered elsewhere.

The current interest is how this array can be reshaped to form other array types and how they appear.

The following method converts the Geo array to an *object* array since the shape of the constituent parts does not have the same shape.

```python
a = sq.as_arrays()

In [2]: a[0]  # a[0][0].shape => (7, 2), a[0][1].shape => (4, 2)
Out[2]: 
array([array([[   0.00,    0.00],
              [   2.00,    8.00],
              [   8.00,   10.00],
              [  10.00,   10.00],
              [  10.00,    8.00],
              [   9.00,    1.00],
              [   0.00,    0.00]]), array([[   3.00,    3.00],
                                           [   7.00,    3.00],
                                           [   5.00,    7.00],
                                           [   3.00,    3.00]])], dtype=object)


In [3]: a[1]  # a[1].shape => (8, 2)
Out[3]: 
array([[   8.00,   10.00],
       [   8.00,   11.00],
       [   8.00,   12.00],
       [  12.00,   12.00],
       [  12.00,    8.00],
       [  10.00,    8.00],
       [  10.00,   10.00],
       [   8.00,   10.00]])

In [4]: a[2]  # a[2].shape => (6, 2)
Out[4]: 
array([[   5.00,   10.00],
       [   5.00,   12.00],
       [   6.00,   12.00],
       [   8.00,   12.00],
       [   8.00,   11.00],
       [   5.00,   10.00]])

In [5]: a[3]  # a[3].shape => (5, 2)
Out[5]: 
array([[   5.00,   12.00],
       [   5.00,   15.00],
       [   7.00,   14.00],
       [   6.00,   12.00],
       [   5.00,   12.00]])

In [6]: a[0].dtype  # dtype('O')

In [7]: a[0][0].dtype, a[0][1].dtype  # (dtype('float64'), dtype('float64'))

In [8]: a[1].dtype  # dtype('float64')

In [9]: a[2].dtype  # dtype('float64')

In [10]: a[3].dtype  # dtype('float64')
```

The shape and dtype of the array depends on the part being examined.  The first array (a[0]) consists of two parts, an outer ring in clockwise order and an inner ring in counterclockwise order (a hole).  The shape of both parts is different, hence, the combination results in an object array, whereas the individual constituents are floating point arrays.

The remaining parts of the array are all singlepart arrays of the same dtype, but with different shapes.

Here it is.

<a href="url"><img src="sq.png" align="left" height="auto" width="300"></a>

<br clear="all">
