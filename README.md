# DendroCatalogs
This is to store the derived property catalogs of Dendrogram features, based on the DR1 data from the GBT Ammonia Survey (Friesen and Pineda, et al., 2017).

## Technical Notes
* Following Rosolowsky & Leroy (2006) and Rosolowsky et al. (2008), three different weighting schemes are adopted when deriving the physical properties.  These are:

    1. *Bijection*: This includes all emission within the boundary of a mask, usually defined by a constant emission level.  The numbers returned by `astrodendro.analysis` are based on this weighting.

    2. *Clipping*: This assumes that only emission above the boundary of a mask defined by a constant emission level is related.  When this scheme is adopted, the weights used to derive the size, the line width, and the flux are the original emission levels minus the emission level used to define the boundary.  (w_i = T_i - T_edge, in Rosolowsky & Leroy (2006).)

    3. *Extrapolation*: This takes the emission within the boundary of a mask and extrapolate out to T = 0K to estimate the "real" physical properties.  Following Rosolowsky & Leroy (2006), the size and the line width are extrapolated by fitting to a linear function, and the flux is extrapolated by fitting to a quadratic function.

See Fig. 4 in Rosolowsky et al. (2008) for image representations of these three weighting schemes.
* Parameters other than the three (the size, the line width, and the flux) mentioned in Rosolowsky & Leroy (2006) are derived in each scheme based on two principles:

    1. Averaged values based on the property maps are weighted by the inverse squared uncertainty of each property.

    2. Quantities with units related to or scaled by the basic properties (the size, the line width, and the flux) are derived according to such scalings.  For example, in the "extrapolation" scheme, the mass based on the Herschel column density map is derived by scaling the mass within the projected dendrogram mask with a factor equal to the ratio between the flux in the "bijection" scheme and the flux in the "extrapolation" scheme.  See the list of physical properties below for mathematical definitions.

* There are two versions of the setup parameters of the Dendrogram algorithm:

    1. The first setup has a minimum number of pixels set to 100 (`min_npix = 100`), with the rest of the setups chosen to be the same as used by Friesen et al. (2016).  This increase of `min_npix` from "one beam and two channels" (essentially 18 pixes) is used for the implementation of the three weighting schemes.  This is particularly critical in the extrapolation scheme, where the "shape" of the emission within the boundary is used to in the extrapolation.  Lack of enough sample points for the "shape" could pose a serious problem to the derived values in the "extrapolation" scheme.

    2. The second setup strictly follows the values used to identify the "features" in these catalogs are chosen to be the same as Friesen et al. (2016).

    3. The second setup strictly follows the values used to identify the "features" in these catalogs are chosen to be the same as Friesen et al. (2016).

* The velocity gradient fitting and the estimation of the specific angular momentum ("j") are based on Goodman et al. (1993).  The same methods are also used by Dib et al. (2010) and Yen et al. (2011), except different assumptions about the inclination.
* The `astrodendro` package is used to identify features.  See http://dendrograms.org for details.
* The `SCIMES` package is used to cluster dendrogram features.  See http://scimes.readthedocs.io for details.
* The catalogs are csv files produced by the `pandas` package, from `pandas.DataFrame` objects.

## File Organization in this Repo
* The `catalogs` folder stores the catalogs.  The catalog files are named by the region name followed by the weighting scheme.  In the `archive` folder are catalogs derived from dendrograms with setups strictly the same as in Friesen et al. (2016).  Only the "bijection" scheme (the default of `astrodendro.analysis`) is used for the catalogs in the `archive`.
* The `dendrograms` folder stores the dendrograms used to identify the structures in the catalogs.  Similarly, the dendrograms in the `archive` folder are derived with the same setups as used by Friesen et al. (2016).
* The `results` folder stores the visualizations of the catalogs.  They are not meant to be exhaustive, though.

## Use the Catalogs
The column keys and the corresponding physical quantities are as follows:

1. `ID`: the ID assigned by the `astrodendro` package to each of the dendrogram feature.  This is consistent through all three catalogs (using each of the three weighting schemes) of the same region.
2. `RA`: the right ascension of the intensity-weighted centroid. [deg.]

    * Bijection: centroids along the R.A. direction, weighted by the emission within the boundary of the dendrogram features.

    * Clipping: centroids along the R.A. direction, weighted by the emission minus the emission level at the boundary.

    * Extrapolation: same as in the "bijection" scheme.

3. `Dec`: the declination of the intensity-weighted centroid. [deg.]

    * Same as `RA`, but along the Dec. direction.

4. `M`: the mass, based on the Herschel column density map and the projection of each dendrogram feature on the plane of the sky. [M_sun]

    * Bijection: sums of mass within the projected boundary on the plane of the sky, in the Herschel column density maps.

    * Clipping: sums of mass within the projected boundary on the plane of the sky, scaled by the ratio between the "clipping" flux and the "bijection" flux (Mass_c = Mass_b * F_c/F_b).

    * Extrapolation: sums of mass within the projected boundary on the plane of the sky, scaled by the ratio between the "extrapolation" flux and the "bijection" flux (Mass_e = Mass_b * F_e/Fb).

5. `Axis_maj`: the intensity-weighted second moment along the "major axis" identified by PCA. [pc]

    * Bijection: the second moment along the major axis (on the plane of the sky) in the PCA analysis of the emission within the boundary.

    * Clipping: the second moment along the major axis in the PCA analysis of the emission within the boundary minus the emission level at the boundary.

    * Extrapolation: the extrapolated second moment at 0K along the major axis, based on measurements at different threshold values (T_edge in Rosolowsky & Leroy (2006)) within the original dendrogram boundary.  In these measurements, T_edge runs from the lowest emission level that is the emission level used to defined the boundary, to the highest emission level within each dendrogram feature.  For dendrogram branches, the highest emission level is where the feature is broken down to two smaller features (the "saddle point").

6. `Axis_min`: the intensity-weighted second moment perpendicular to the "major axis" identified by PCA. [pc]

    * Same as `Axis_maj`, but along the minor axis in the PCA analysis.

7. `R_eff`: the geometric mean of `Axis_maj` and `Axis_min` (in all three schemes).  This is used as the "effective radius" in Friesen et al. (2016). [pc]

8. `PA`: the position angle of the "major axis" identified by PCA.  The angle is in degrees and has a value between 0 and 180, from the north to the east on the plane of the sky. (Notice that this is different from the "position angle" defined in `astrodendro`.) [deg.]

    * Bijection: the position angle of the major axis in the PCA analysis of the emission within the boundary.

    * Clipping: the position angle of the major axis in the PCA analysis of the emission within the boundary minus the emission level at the boundary.

    * Extrapolation: same as in the "bijection" scheme.  No extrapolation is done.

9. `Area`: the exact area of the dendrogram feature projected onto the plane of the sky. [pc^2]

    * Bijection: the exact area of the projected mask on the plane of the sky.

    * Clipping: same as the "bijection" scheme.

    * Extrapolation: the exact area of the projected mask scaled by the squared ratio between the "extrapolation" effective radius and the "bijection" effective radius (Area_e = Area_b * (R_e/R_b)^2).

10. `V_cen`: the intensity-weighted velocity centroid. [km/s]

    * Bijection: centroids along the velocity direction, weighted by the emission within the boundary of the dendrogram features.

    * Clipping: centroids along the velocity direction, weighted by the emission minus the emission level at the boundary.

    * Extrapolation: same as in the "bijection" scheme.

11. Three different ways to measure the velocity dispersion:

    * `V_RMS`: average of the width in the property fits,  weighted by the inverse squared uncertainties. [km/s]  This is the same for all three schemes.
    * `V_RMS_dendro`: the velocity dispersion reported by `astrodendro`. [km/s]

        * Bijection: the second moment along the velocity axis, of all the emission within the boundary.

        * Clipping: the second moment along the velocity axis, of the emission within the boundary minus the emission level at the boundary.

        * Extrapolation: the extrapolated second moment at 0K along the velocity axis, based on measurements at different threshold values (T_edge in Rosolowsky & Leroy (2006)) within the original dendrogram boundary.  In these measurements, T_edge runs from the lowest emission level that is the emission level used to defined the boundary, to the highest emission level within each dendrogram feature.  For dendrogram branches, the highest emission level is where the feature is broken down to two smaller features (the "saddle point").

    * `V_RMS_refit`: the velocity dispersion from a Gaussian fit of the average spectrum derived from the emission within the boundary.

        * Bijection: the width of the Gaussian fitted to the average spectrum is derived from all the emission within the boundary.

        * Clipping: the width of the Gaussian fitted to the average spectrum is derived from the emission within the boundary minus the emission level at the boudnary (w_i = T_i - T_edge).

        * Extrapolation: the extrapolated Gaussian width based on fits to the average spectra derived from emission within varying thresholds above the original boundary.  In these measurements, T_edge runs from the lowest emission level that is the emission level used to defined the boundary, to the highest emission level within each dendrogram feature.  For dendrogram branches, the highest emission level is where the feature is broken down to two smaller features (the "saddle point").

12. `V_disp`: the total velocity dispersion.  See Equation 3 in Friesen et al. (2016). [km/s]  Three flavors, with corresponding name endings (i.e., nothing, with `_dendro`, and with `_refit`), are derived based on the above three different ways to derive `V_RMS`.
13. `V_disp_nt`: the non-thermal velocity dispersion. [km/s]  Three flavors, with corresponding name endings (i.e., nothing, with `_dendro`, and with `_refit`), are derived based on the above three different ways to derive `V_RMS`.
14. `V_grad_abs`: the velocity gradient magnitude.  The value is derived assuming sin(i) = 1, same as in Goodman et al. (1993) and different from sin(i) = pi/4 in Dib et al. (2010).  This means that the values in these catalogs would be higher than the values derived by Dib et al. (2010) by a factor of ~ 1.27.  In the associated plots, the values are multiplied by 1.27 to be compared directly to the values in Dib et al. (2010).  Yen et al. (2011) used a specific value of i = 10 deg. for B335, based on independent interferometric observations (Hirano et al., 1998). [km/s/pc]  This is the same in all three schemes.
15. `V_grad_PA`: the position angle of the velocity gradient.  The velocity gradient has a direction pointing from the (more) blue-shifted side to the (more) red-shifted side.  The position angle has a value between 0 and 360, from the North to the East on the plane of the sky.  Notice that the value domain is different from that of the position angle of the major axis, because that the velocity gradient is directional. [deg.]  This is the same in all three schemes.
16. `j`: the specific angular momentum. [pc km/s]  This is derived from the velocity gradient, and thus is the same in all three schemes.
17. `M_vir`: the virial mass, with p = 1.5.  See Equation 1 and 2 in Friesen et al. (2016). [M_sun]  Three flavors, with corresponding name endings (i.e., nothing, with `_dendro`, and with `_refit`), are derived based on the above three different ways to derive `V_RMS` (and thus the velocity dispersions).
18. `virial`: the virial parameter, which is simply the virial mass divided by the actual mass. [dimensionless]  Three flavors, with corresponding name endings (i.e., nothing, with `_dendro`, and with `_refit`), are derived based on the above three different ways to derive `V_RMS` (and thus the velocity dispersions).
19. `m_lin`: the linear "mass," which is in the unit of M_sun/pc.  This calculated by dividing the total mass by the FWHM along the major axis (FWHM_{maj} = 2 sqrt(2ln(2)) sigma_{maj}).  See Equation 4 in Friesen et al. (2016). [M_sun/pc]
20. `m_lin_vir`: the virial linear "mass."  This is estimated according to Equation 4 in Friesen et al. (2016). [M_sun/pc]  Three flavors, with corresponding name endings (i.e., nothing, with `_dendro`, and with `_refit`), are derived based on the above three different ways to derive `V_RMS` (and thus the velocity dispersions).
21. `virial_lin`: the linear virial parameter, which is simply `m_lin_vir` divided by `m_lin`. [dimensionless]  Three flavors, with corresponding name endings (i.e., nothing, with `_dendro`, and with `_refit`), are derived based on the above three different ways to derive `V_RMS` (and thus the velocity dispersions).
22. Four different identifiers for the dendrogram features:

	* `id_unresolved`: indicating an unresolved dendrogram feature.  That is, the feature has a projected area smaller than a beam size and a "width" in velocity less than two velocity channels.
	* `id_resolvedLeaves`: indicating a resolved leaf structure.  A leaf structure is a dendrogram feature that *cannot* be further divided into smaller pieces according to the setup parameters.
	* `id_resolvedBranches`: indicating a resolved branch structure.  A branch structures is a dendrogram feature that *can* be further divided.  The above three (boolean) indicators are mutually exclusive.
	* `id_SCIMES`: the dendrogram ID of the bottom most dendrogram feature that connects all dendrogram features that are recognized by the `SCIME` package to belong to the same "cluster."  See http://scimes.readthedocs.io for more information.

23. `abundance_pNH3`: the total NH_3 mass derived from the property map divided by the Herschel-based total mass. [dimensionless]  Since the scalings (see `M`) are the same for both the NH_3 mass and the Herschel-based mass, the abundance, which is a ratio between the two quantities, is the same in all three schemes.
24. `Tkin`: the kinetic temperature from the NH_3 fit, averaged over the projection of each dendrogram feature and weighted by the inverse squared uncertainty. [K]
25. `Tdust`: the dust temperature from the Herschel SED fit, averaged over the projection of each dendrogram feature. [K]  Since no pixel-by-pixel uncertainty is known about the Herschel SED fits, the weighting is constant throughout all pixels.

***For L1688 in Ophiuchus ONLY***, the numbers of c2d and SCUBA2 sources within the projection of each dendrogram feature are also calculated.  They are in `count_c2d` and `count_SCUBA2`.

## Glue the Catalogs
Using `Glue` to explore the data is highly recommended.  See http://glueviz.org for information about installation and data exploration.

When opening the catalogs from within `Glue`, please select "Pandas Table" in the drop-down file type menu.

## Version Notes
* The current version is not by any means final products.
* The code(s) used to derive the property catalogs will be made public after testing.
