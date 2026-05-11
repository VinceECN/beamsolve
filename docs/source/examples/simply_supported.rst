Simply supported beam
---------------------

Modal analysis of a simply supported aluminium beam: FEM vs analytical solution.

System description
^^^^^^^^^^^^^^^^^^

The beam is pinned at both ends (displacement zero, rotation free) and has length :math:`L`,
Young's modulus :math:`E`, density :math:`\rho`, and a circular cross-section of diameter :math:`d`.

For a pinned-pinned beam the exact wavenumbers are :math:`\beta_n L = n\pi`, giving natural frequencies

.. math::
   f_n = \frac{(n\pi)^2}{2\pi L^2} \sqrt{\frac{EI}{\rho A}}, \quad n = 1, 2, 3, \ldots

Code description
^^^^^^^^^^^^^^^^

The script below

- Constructs the beam using :func:`beamsolve.beam.beam.circular_section` and :class:`beamsolve.beam.beam.Beam`.
- Solves the eigenvalue problem using :class:`beamsolve.FEM.fem_model.FEMModel` and :meth:`beamsolve.FEM.fem_model.FEMModel.solve_modes`.
- Computes the exact solution using :class:`beamsolve.Analytical.analytical_model.AnalyticalModel`.
- Compares FEM and analytical natural frequencies.
- Plots both sets of mode shapes.

.. literalinclude:: ../../../examples/simply_supported.py
   :language: python
