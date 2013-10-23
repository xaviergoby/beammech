Introduction
============

Over the years I've written several programs in languages from Perl to Lua to
solve the differential equations for pure bending y'' = M/(E·I) and shear
y' = α·V/(G·A) of beams. The solution is determined by integration. 

For the simple cases of pure bending (where E·I is constant over the length of
the beam, and where shear deformation is ignored), the solution can be found
in reference books. Combining them for different loads can be rather
cumbersome,though.

However, in practice I usually deal with cases where E·I (and G·A) vary along
the length of the beam, where there may be multiple loads presen and where
shear deflection cannot be ignored. So I had to calculate my own
solutions. These programs all followed the same pattern. So I decided to
separate the re-usable parts and put them in a module. That was the birth of
beammech.py.

This software was written with metric units in mind. So forces are in N, and
lengths in mm. A standard Cartesian coordinate system is used, with the x-axis
pointing to the right from 0 and the y-axis pointing up. The length of each
segment dx for summation (integration) is 1 mm, because that makes the math a
whole lot simpler. It is also a good match for the kind of problems I use it
for.

Using beammech.py
=================

The first task is to define a function that gives the cross-section properties
along the length of the beam. It takes a single integer argument indicating
the position along the beam, and should return a four-tuple containing the
second area moment (I), shear stiffness (G·A) and the distances from the
neutral line to the top and bottom of the beam at that position respectively.

The next task is to create a tuple containing the positions of the two simple
supports of the beam. Then a list of loads (objects that inherit from the Load
class). These two, along with the length of the beam, are used as arguments to
the beammech.shearforce function. This returns a list of the shear force at
every millimeter along the length of the beam.

This list of shear forces, along with the Young's Modulus of the beam's
material and the function for producing the cross-sectional properties and the
supports are used as input to the beammech.loadcase function. This returns a
tuple of four lists; the bending moment in the cross-section, the deflection
of the beam and the stress at the top and bottom of the beam. These form the
solution of the load case. With regard to integrating the shear force into the
bending moment, the assumption is made that the bending moment at 0 (which is
the left end of the beam) is 0.

This solution strategy only allows for two supports, resulting in statically
determined cases. Statically indetermined cases can be calculated by adding
the additional supports as loads, and manipulating them until the deflection
at the load points is 0.