Cvxopt
======

``Cvxopt`` provides many routines for solving convex optimization
problems such as linear and quadratic programming packages. It also
has a very nice sparse matrix library that provides an interface to
``umfpack`` (the same sparse matrix solver that ``matlab`` uses), it also
has a nice interface to ``lapack``. For more details on ``cvxopt`` please
refer to its documentation at `<http://cvxopt.org/userguide/index.html>`_

Sparse matrices are represented in triplet notation that is as a
list of nonzero values, row indices and column indices. This is
internally converted to compressed sparse column format. So for
example we would enter the matrix

.. math::

   \left(
   \begin{array}{ccccc}
   2&3&0&0&0\\
   3&0&4&0&6\\
   0&-1&-3&2&0\\
   0&0&1&0&0\\
   0&4&2&0&1
   \end{array}\right)

by

::

    sage: import numpy                           # optional - cvxopt
    sage: from cvxopt.base import spmatrix       # optional - cvxopt
    sage: from cvxopt.base import matrix as m    # optional - cvxopt
    sage: from cvxopt import umfpack             # optional - cvxopt
    sage: Integer = int                          # optional - cvxopt
    sage: V = [2,3, 3,-1,4, 4,-3,1,2, 2, 6,1]    # optional - cvxopt
    sage: I = [0,1, 0, 2,4, 1, 2,3,4, 2, 1,4]    # optional - cvxopt
    sage: J = [0,0, 1, 1,1, 2, 2,2,2, 3, 4,4]    # optional - cvxopt
    sage: A = spmatrix(V,I,J)                    # optional - cvxopt

To solve an equation :math:`AX=B`, with :math:`B=[1,1,1,1,1]`,
we could do the following.

.. link

::

    sage: B = numpy.array([1.0]*5)               # optional - cvxopt
    sage: B.shape=(5,1)                          # optional - cvxopt
    sage: print(B)                               # optional - cvxopt
    [[1.]
     [1.]
     [1.]
     [1.]
     [1.]]


    sage: print(A)                               # optional - cvxopt
    [ 2.00e+00  3.00e+00     0         0         0    ]
    [ 3.00e+00     0      4.00e+00     0      6.00e+00]
    [    0     -1.00e+00 -3.00e+00  2.00e+00     0    ]
    [    0         0      1.00e+00     0         0    ]
    [    0      4.00e+00  2.00e+00     0      1.00e+00]
    sage: C = m(B)                               # optional - cvxopt
    sage: umfpack.linsolve(A,C)                  # optional - cvxopt
    sage: print(C)                               # optional - cvxopt
    [ 5.79e-01]
    [-5.26e-02]
    [ 1.00e+00]
    [ 1.97e+00]
    [-7.89e-01]

Note the solution is stored in :math:`B` afterward. also note the
m(B), this turns our numpy array into a format ``cvxopt`` understands.
You can directly create a cvxopt matrix using cvxopt's own matrix
command, but I personally find numpy arrays nicer. Also note we
explicitly set the shape of the numpy array to make it clear it was
a column vector.

We could compute the approximate minimum degree ordering by doing

::

    sage: RealNumber = float                     # optional - cvxopt
    sage: Integer = int                          # optional - cvxopt
    sage: from cvxopt.base import spmatrix       # optional - cvxopt
    sage: from cvxopt import amd                 # optional - cvxopt
    sage: A = spmatrix([10,3,5,-2,5,2],[0,2,1,2,2,3],[0,0,1,1,2,3])  # optional - cvxopt
    sage: P = amd.order(A)                       # optional - cvxopt
    sage: print(P)                               # optional - cvxopt
    [ 1]
    [ 0]
    [ 2]
    [ 3]

For a simple linear programming example, if we want to solve

.. math::

   \begin{array}{cc}
   \text{minimze} & -4x_1-5x_2\\
   \text{subject to} & 2x_1 +x_2\le 3\\
                     & x_1+2x_2\le 3\\
                     & x_1 \ge 0 \\
                    & x_2 \ge 0\\
   \end{array}


::

    sage: RealNumber = float                     # optional - cvxopt
    sage: Integer = int                          # optional - cvxopt
    sage: from cvxopt.base import matrix as m    # optional - cvxopt
    sage: from cvxopt import solvers             # optional - cvxopt
    sage: c = m([-4., -5.])                      # optional - cvxopt
    sage: G = m([[2., 1., -1., 0.], [1., 2., 0., -1.]])  # optional - cvxopt
    sage: h = m([3., 3., 0., 0.])                # optional - cvxopt
    sage: sol = solvers.lp(c,G,h)       # random # optional - cvxopt
         pcost       dcost       gap    pres   dres   k/t
     0: -8.1000e+00 -1.8300e+01  4e+00  0e+00  8e-01  1e+00
     1: -8.8055e+00 -9.4357e+00  2e-01  1e-16  4e-02  3e-02
     2: -8.9981e+00 -9.0049e+00  2e-03  1e-16  5e-04  4e-04
     3: -9.0000e+00 -9.0000e+00  2e-05  3e-16  5e-06  4e-06
     4: -9.0000e+00 -9.0000e+00  2e-07  1e-16  5e-08  4e-08

.. link

::

    sage: print(sol['x'])    # optional - cvxopt  # ... below since can get -00 or +00 depending on architecture
    [ 1.00e...00]
    [ 1.00e+00]
