## Clipping

<img src="../images/clip_poly0to9.png" align="right" width="400"/>


Clip features


```
# intersections            poly clip
#          X       Y        ID   ID
array([[  0.00,   7.50],   [ 0,   0],
       [  0.00,   7.50],   [ 0,   3],
       [  9.17,  10.00],   [ 1,   0],
       [  2.00,   5.62],   [ 4,   3],
       [  2.13,   5.50],   [ 5,   3],
       [  3.73,   4.00],   [ 7,   3],
       [  9.78,   2.00],   [ 9,   2],
       [  5.87,   2.00],   [ 9,   3],
       [  8.00,   0.00],   [11,   2],
       [  8.00,   0.00]]), [11,   3]

```

```
def p_c_p(clipper, poly):
    """intersect poly features.

    Parameters
    ----------
    clipper, poly : array_like
         Clockwise-ordered sequence of points (x, y) with the first and last
         point being the same. The `clipper` is the polygon or polylines
         cutting the `poly` geometry.
    """
    p_cl, c_cl = [i.XY if hasattr(i, "IFT") else i for i in [poly, clipper]]
    x3_x2, y3_y2 = (p_cl[1:] - p_cl[:-1]).T
    dc = c_cl[1:] - c_cl[:-1]
    dc_x = dc[:, 0][:, None]
    dc_y = dc[:, 1][:, None]
    pc02 = p_cl[:-1] - c_cl[:-1][:, None]
    pc02_x = pc02[..., 0]
    pc02_y = pc02[..., 1]
    # --
    d_nom = (x3_x2 * dc_y) - (y3_y2 * dc_x)
    a_0 = pc02_y * dc_x
    a_1 = pc02_x * dc_y
    b_0 = x3_x2 * pc02_y
    b_1 = y3_y2 * pc02_x
    a_num = a_0 - a_1  # (p02_y * dc_x) - (p02_x * dc_y)
    b_num = b_0 - b_1  # (x3_x2 * p02_y) - (y3_y2 * p02_x)
    with np.errstate(all='ignore'):  # divide='ignore', invalid='ignore'):
        u_a = a_num/d_nom
        u_b = b_num/d_nom
        z0 = np.logical_and(u_a >= 0., u_a <= 1.)
        z1 = np.logical_and(u_b >= 0., u_b <= 1.)
        both = z0 & z1
        xs = u_a * x3_x2 + p_cl[:-1][:, 0]
        ys = u_a * y3_y2 + p_cl[:-1][:, 1]
    #  whre = np.vstack(both.nonzero()).T
    # bothT = both.T
    # xs = xs.T[bothT]
    # ys = ys.T[bothT]
    # poly_clipper_ids = np.vstack(np.nonzero(bothT)).T
    xs = xs[both]
    ys = ys[both]
    pc_ids = np.vstack(both.nonzero()).T
    if xs.size > 0:
        final = np.zeros((len(xs), 2))
        final[:, 0] = xs
        final[:, 1] = ys
        return final, both, pc_ids  # poly_clipper_ids
    return None, both, None
