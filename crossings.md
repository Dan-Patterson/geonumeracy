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

    A portion of a `line`/`polyline`.  The connection between two points.  The basic
- 
**Two equations with two unknowns**

```python                                                                                                 numerators
     # the t_numer ==> ua                       Just arranged like line_crossings  a =         a_0    <=  a_1
     (x3 - x2)(y0 - y2) - (y3 - y2)(x0 - x2)   (y0 - y2)*dc_x - (x0 - x2)*dc_y     a = (y0 - y2)*dc_x <= (x0 - x2)*dc_y
ua = --------------------------------------  = --------------------------------- =      
     (y3 - y2)(x1 - x0) - (x3 - x2)(y1 - y0)   (x1 - x0)*dc_y - (y1 - y0)*dc_x   

     # the s_numer ==> ub                                                          b =         b_0    <=   b_1
     (x1 - x0)(y0 - y2) - (y1 - y0)(x0 - x2)   (y1 - y2)*dc_x - (x1 - x2)*dc_y     b = (y1 - y2)*dc_x <= (x1 - x2)*dc_y
ub = --------------------------------------  = --------------------------------- = 
     (y3 - y2)(x1 - x0) - (x3 - x2)(y1 - y0)   (x1 - x0)*dc_y - (y1 - y0)*dc_x

denominator = (y3 -y2)*(x1 - x0) - (x3 - x2) * (y1 - y0) = (x1 - x0)*dc_y - (y1 - y0)*dc_x

x = x0 + ua (x1 - x0)
y = y0 + ub (y1 - y0)

```
