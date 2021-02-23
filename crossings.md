# Crossings



Points, lines, segments.  The basic entities.

Inside, outside, on, equal.  The basic questions.

## Background

This section will lay the foundational terminology for this and subsequent sections.  The reference section contains links to those that I have found most useful.  It is not a complete or comprehensive list.

**Terms**

- point

    A location denoted by X, Y coordinates.  I only work with coordinates which are planar.  For the maths, think of an X,Y graph.  For those in the spatial fields, think `projected coordinates`, like UTM.

    Points are the basic building block of geometry.  I will ignore the `Z` axis/dimension for now.  If `X` and `Y` are used for location, then consider `Z` to represent height or some other measureable attribute for that location. 

- segment

    A portion of a `line`/`polyline`.  The connection between two points.  The second building block for polylines and polygons.
    
 - polyline and polygon

   I will use the terms `polyline` and `polygons` throughout.  The former represents the perimeter of the latter.

**Notations**

Points are represented by a numpy Nx2 ndarray.  For example, this is a square.  The first column is the X-coordinates and the second column is the Y-coordinates.

```
pnts # -- An array of points
array([[  0.00,   0.00],
       [  0.00,  10.00],
       [ 10.00,  10.00],
       [ 10.00,   0.00],
       [  0.00,   0.00]])
seg # -- the first segment of pnts showing from-to points
array([[  0.00,   0.00],
       [  0.00,  10.00]])

seg_ravel
array([  0.00,   0.00,   0.00,  10.00])  # -- same as seg, but with the pairs raveled/flattened

```
The array need not represent anything other a series of points.  In this particular case, it was used to represent a polyline and/or a polygon.
Why?  The points are ordered in clockwise order and the first and last points are the same.  Others use counter-clockwise order to represent poly/* geometries, but personally... I found it counter-productive ;).


```
pnts    - The points used to represent geometry.
x, y    - A generic point or series of points.  
          If `pnts` above, represented any set of points, then `x,y` would be used to represent their coordinates.
          If the points belonged to a segment, polyline or polygon, then one of the notations that follow would be
          used to indicate their special condition.
x0, y0  - The first two coordinates in `seg` or `seg_ravel`.  These represent the first point in a segment.
x1, y1  - The second two coordinates, representing the last point in the segment.
x2, y2  - As above, but for another segment
x3, y3
```
Now... having said the above.  I am rarely working with 2 segments or 4 points.
I consider `x0, y0` to represent all the x,y coordinates for all the primary segments I am studying.

If I wanted to find the intersections between 100 segments in one data set and 1000 segments in another data set, then

  - `x0,y0 and x1,y1` would represent the start and end points for the first data set (100 segments) and
  - `x2,y2 and x3,y3` would be those of the second data set (1000 segments).

If you are with me, then press on.

**Two equations with two unknowns**

```python                                                                                                 
     # the t_numer ==> ua                            alternate notation
                                                     dc_x = (x3 - x2)
                                                     dc_y = (y3 - y2)
     (x3 - x2) * (y0 - y2) - (y3 - y2) * (x0 - x2)   (y0 - y2) * dc_x - (x0 - x2) * dc_y
ua = --------------------------------------------  = ------------------------------------
     (y3 - y2) * (x1 - x0) - (x3 - x2) * (y1 - y0)   (x1 - x0) * dc_y - (y1 - y0) * dc_x

     # the s_numer ==> ub
     (x1 - x0) * (y0 - y2) - (y1 - y0) * (x0 - x2)   (y1 - y2) * dc_x - (x1 - x2) * dc_y
ub = --------------------------------------------  = ------------------------------------
     (y3 - y2) * (x1 - x0) - (x3 - x2) * (y1 - y0)   (x1 - x0) * dc_y - (y1 - y0) * dc_x

denominator = (y3 - y2) * (x1 - x0) - (x3 - x2) * (y1 - y0)
#           = (x1 - x0) * dc_y - (y1 - y0)  * dc_x

# numerator tests

a0 = (y0 - y2) * dc_x  # -- from ua
a1 = (x0 - x2) * dc_y
a = a_0 <= a_1

b0 = (y1 - y2) * dc_x  # -- from ub
b1 = (x1 - x2) * dc_y
b = b_0 <= b_1

x = x0 + ua * (x1 - x0)
y = y0 + ub * (y1 - y0)

```



References
----------
[Paul Bourke geometry](http://paulbourke.net/geometry/pointlineplane/)
