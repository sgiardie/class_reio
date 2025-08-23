CLASS\_REIO: an implementation of extended reionization models in the Cosmic Linear Anisotropy Solving System
================================================================================================================

**Original CLASS authors**: Julien Lesgourgues and Thomas Tram

**Reionization extension author**: Stéphane Ilic

Compiling CLASS\_REIO and getting started
-----------------------------------------

The process of compiling and running CLASS\_REIO is strictly identical to the original CLASS code. Please refer to the CLASS code [webpage](http://class-code.net) and [GitHub repository](https://github.com/lesgourg/class_public) for detailed instructions.

Extended reionization models implementation
-------------------------------------------

CLASS\_REIO is a modification of the CLASS code which adds several phenomenologically-motivated reionization schemes beyond the standard tanh-based parameterization. The current implementation allows the user to choose from two new reionization parameterizations (in addition to the original options):

* **Two-stage asymmetric reionization (`reio_asymm`):** A reionization history characterized by a gradual onset and a rapid completion (or vice versa). It is defined by a starting redshift (`reio_asymm_zbeg`), an ending redshift (`reio_asymm_zend`), and an asymmetry parameter (`reio_asymm_alpha`) controlling the steepness of the transition. This two-staged model provides a more flexible fit to an extended reionization epoch.
* **Flexible knot-based reionization (`reio_flexknot`):** A free-form reionization history described by a number of “knot” points in redshift and \$x_e\$. The code constructs a smooth, continuously-differentiable ionization history by connecting these knots with so-called Piecewise Cubic Hermite Interpolating Polynomials (PCHIP), which also guarantee a monotonous evolution between knots. The user specifies the number of knots (`reio_flexknot_num`) and their list of values (`reio_flexknot_z` and `reio_flexknot_xe`). A non-increasing list of redshifts will yield an error by default, unless the `reio_flexknot_reorder_z` parameter is set to be nonzero in which case the code will reorder the provided knots by increasing redshifts. Finally, setting the `reio_flexknot_smooth_start` parameter to be nonzero forces the slope of \$x_e(z)\$ to be zero at the initial (i.e highest-redshift) knot.

Additional reionization-related options
---------------------------------------

CLASS\_REIO comes with a few additional features regarding new and old reionization schemes.

Each of the two new reionization schemes -- as well as the original `reio_inter` (linear interpolation between \$x_e(z)\$  points) -- also supports an optional helium reionization contribution. If enabled via their respective `{model_name}_add_HeII_tanh` parameter (i.e. set to nonzero), the code will include a secondary reionization of singly-ionized helium (HeII->HeIII) at a specified redshift with a tanh-like transition of given width, using the same original CLASS parameters `helium_fullreio_redshift` and `helium_fullreio_width` for the treatment of HeII. This allows the helium reionization (occurring at \$z \sim 3\$) to be consistently incorporated in all reionization models.

The `reio_many_tanh`, `reio_inter`, and `reio_flexknot` models also now include an additional option, `{model_name}_xe_scheme`, which allows users to choose how the input ionization fraction (\$x_e\$) values are interpreted:
- if set to 0: uses the standard interpretation of the original `reio_many_tanh` and `reio_inter` models
- if set to 1: applies a piecewise linear mapping from 0->1->2 to 0->(1 + fHe)->(1 + 2*fHe), where `fHe` is the helium fraction.
- any other value: applies a piecewise linear mapping from 0->1.08->1.16 to 0->(1 + fHe)->(1 + 2*fHe).

These new mappings help constrain the exploration of parameter space to physical models. Moreover, for the `reio_many_tanh` model, the `many_tanh_width` parameter has been modified to accept an array of widths, allowing for individual control over the sharpness of each tanh transition.

Additionally, CLASS_REIO introduces several new miscellaneous parameters to enhance flexibility in reionization modeling and analyzing:
* Users can now calculate additional optical depths over arbitrary redshift intervals by specifying the number of requested optical depths (`reio_taus_num`, up to 100), along with lists of lower (`reio_taus_zmin`) and upper (`reio_taus_zmax`) redshift bounds for each interval.
* The interpolation method for reionization quantities can be changed from the standard spline to a Piecewise Cubic Hermite Interpolating Polynomial (PCHIP) by setting `reio_interp_type` to a nonzero value; this is particularly useful to avoid unphysical values of the ionization fraction which can result from the defaut spline interpolation of CLASS, when dealing with sharp transitions in the reionization history.
* The upper redshift bound used in the calculation of the reionization optical depth (`tau_reio`) can be overridden by providing a value for `reio_zmax_calc_tau`, replacing the default choice (the global minimum of the reionization fraction).
* The method for calculating `tau_reio` can also be switched to use the integral of a PCHIP-interpolated reionization history by setting `calc_tau_method` to nonzero; when choosing this option, the lower redshift bound for the calculation can be set with `reio_zmin_calc_tau` (default is z=0).
* Finally, the `recalc_tau` parameter, if set to nonzero, forces the recomputation of `tau_reio` as a derived parameter even when it is provided as an input by the user. 

A thorough description of all CLASS input parameters is available in the `explanatory.ini` file (which serves as a reference guide). The new input parameters introduced in CLASS\_REIO (for selecting and configuring the above reionization models) are clearly marked with a "NEW" tag in that file for easy identification. We recommend consulting this file to understand the usage and default values of each new parameter.

## Python wrapper

Similarly to the original CLASS code, a Python wrapper is included in CLASS\_REIO, which is automatically compiled and installed (under the name `classy_reio`) at the same time as the main code, provided you use the `make` command and not simply `make class`. The only significant change in the wrapper is the addition of the `taus_reio` function, which returns the list of (optional) additional optical depths over the redshift intervals specified by the user (see previous section). Since no other modifications were made to this wrapper, we refer to the original CLASS documentation for details on using the Python interface and its compilation.

## Developing the code

Participation in the further development of the code is welcome. Feel free to clone this repository, create your own development branch, and eventually propose to merge it into the public distribution. You may also discuss new features or improvements you would like to see implemented by opening an issue in this repository.

## License

The CLASS\_REIO project is licensed under the MIT License, in line with the licensing of the original CLASS modifications. See the LICENSE file for more details.

## Code of conduct

While CLASS (and by extension CLASS\_REIO) is free to use, its authors request that publications using the code cite a few publications (see the [original CLASS repository](https://github.com/lesgourg/class_public)). Additionally, we request that users of this modified code acknowledge the CLASS\_REIO extension in their publications and cite the following reference: [Ilic et al., 2025](https://arxiv.org/abs/2504.13254).

## Support

For support related to the main CLASS code, please open a new issue on the [original CLASS repository](https://github.com/lesgourg/class_public). For any issues or questions specifically concerning the reionization module implementation in CLASS\_REIO, please feel free to [open an issue](https://github.com/s-ilic/class_reio/issues) in this repository. We will do our best to address and resolve problems related to the new reionization features.
