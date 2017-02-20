# DendroCatalogs
This is to store the derived property catalogs of Dendrogram features, based on the DR1 data from the GBT Ammonia Survey (Friesen &amp; Pineda, et al., 2017).

## Technical Notes
* The setup parameters of the Dendrogram algorithm used to identify the "features" in these catalogs are chosen to be the same as Friesen et al. (2016).
* The velocity gradient fitting and the estimation of the specific angular momentum ("j") are based on Goodman et al. (1993).  The same methods are also used by Dib et al. (2010) and Yen et al. (2011).
* The `astrodendro` package is used.  See http://dendrograms.org for details.
* The `SCIMES` package is used. See http://scimes.readthedocs.io for details.
* The catalogs are csv files produced by the `pandas` package, from `pandas.DataFrame` objects.

## Use the Catalogs
The column keys and the corresponding physical quantities are as follows:

1. `ID`: the ID assigned by the `astrodendro` package to each of the dendrogram feature.
2. `RA`: the right ascension of the intensity-weighted centroid. [deg.]
3. `Dec`: the declination of the intensity-weighted centroid. [deg.]
4. `M`: the mass, based on the Herschel column density map and the projection of each dendrogram feature on the plane of the sky. [M_sun]
5. `Axis_maj`: the intensity-weighted second moment along the "major axis" identified by PCA. [pc]
6. `Axis_min`: the intensity-weighted second moment perpendicular to the "major axis" identified by PCA. [pc]
7. `R_eff`: the geometric mean of `Axis_maj` and `Axis_min`.  This is used as the "effective radius" in Friesen et al. (2016). [pc]
8. `PA`: the position angle of the "major axis" identified by PCA.  The angle is in degrees and has a value between 0 and 180, from the north to the east on the plane of the sky. (Notice that this is different from the "position angle" defined in `astrodendro`.) [deg.]
9. `Area`: the exact area of the dendrogram feature projected onto the plane of the sky. [pc^2]
10. `V_cen`: the intensity-weighted velocity centroid. [km s^{-1}]
11. `V_RMS`: the intensity-weighted second moment of the velocity. [km s^{-1}]
12. `V_disp`: the total velocity dispersion.  See Equation 3 in Friesen et al. (2016). [km s^{-1}]
13. `V_disp_nt`: the non-thermal velocity dispersion. [km s^{-1}]
14. `V_grad_abs`: the velocity gradient magnitude.  See Goodman et al. (1993), and also Dib et al. (2010) and Yen et al. (2011). [km s^{-1} pc^{-1}]
15. `V_grad_PA`: the position angle of the velocity gradient.  The velocity gradient has a direction pointing from the (more) blue-shifted side to the (more) red-shifted side.  The position angle has a value between 0 and 360, from the North to the East on the plane of the sky.  Notice that the value domain is different from that of the position angle of the major axis, because that the velocity gradient is directional. [deg.]
16. `j`: the specific angular momentum. [km s^{-1} pc]
17. `M_vir`: the virial mass, with $p$ = 1.5.  See Equation 1 and 2 in Friesen et al. (2016). [M_sun]
18. `virial`: the virial parameter, which is simply the virial mass divided by the actual mass. [dimensionless]
19. `m_lin`: the linear "mass," which is in the unit of M_sun pc^{-1}.  This calculated by dividing the total mass by the FWHM along the major axis ($FWHM_{maj} = 2\sqrt{2\ln{2}}\sigma_{maj}$).  See Equation 4 in Friesen et al. (2016). [M_sun pc^{-1}]
20. `m_lin_vir`: the virial linear "mass."  This is estimated according to Equation 4 in Friesen et al. (2016). [M_sun pc^{-1}]
21. `virial_lin`: the linear virial parameter, which is simply `m_lin_vir` divided by `m_lin`. [dimensionless]
22. Four different identifiers for the dendrogram features:

	* `id_unresolved`: indicating an unresolved dendrogram feature.  That is, the feature has a projected area smaller than a beam size and a "width" in velocity less than two velocity channels.
	* `id_resolvedLeaves`: indicating a resolved leaf structure.  A leaf structure is a dendrogram feature that cannot be further divided into smaller pieces according to the setup parameters.
	* `id_resolvedBranches`: indicating a resolved branch structure.  A branch structures is a dendrogram feature that *can* be further divided.  The above three (boolean) indicators are mutually exclusive.
	* `id_SCIMES`: the dendrogram ID of the bottom most dendrogram feature that connects all dendrogram features that are recognized by the `SCIME` package to belong to the same "cluster."  See http://scimes.readthedocs.id for more information.
	
23. `abundance_pNH3`: the total NH$_3$ mass derived from the property map divided by the Herschel-based total mass. [dimensionless]

***For L1688 in Ophiuchus ONLY***, the numbers of c2d and SCUBA2 sources within the projection of each dendrogram feature are also calculated.  They are in `count_c2d` and `count_SCUBA2`.

## Version Notes
* The current version is not by any means final products.
* The code(s) used to derive the property catalogs will be made public after testing.
