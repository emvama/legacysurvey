.. title: Data Release Description
.. slug: description
.. tags: 
.. has_math: yes

.. |sigma|    unicode:: U+003C3 .. GREEK SMALL LETTER SIGMA
.. |sup2|     unicode:: U+000B2 .. SUPERSCRIPT TWO
.. |alpha|      unicode:: U+003B1 .. GREEK SMALL LETTER ALPHA
.. |chi|      unicode:: U+003C7 .. GREEK SMALL LETTER CHI
.. |delta|    unicode:: U+003B4 .. GREEK SMALL LETTER DELTA
.. |deg|    unicode:: U+000B0 .. DEGREE SIGN
.. |times|  unicode:: U+000D7 .. MULTIPLICATION SIGN
.. |plusmn| unicode:: U+000B1 .. PLUS-MINUS SIGN
.. |Prime|    unicode:: U+02033 .. DOUBLE PRIME


Contents of DR1
===============

The DECam Legacy Survey will produce an inference model of the 14,000 square degrees
of extragalactic sky visible from the northern hemisphere in three optical bands
(g,r,z) and four infrared bands.  The sky coverage is approximately bounded by
-18\ |deg| < |delta| < +84\ |deg| in celestial coordinates and :math:`|b|` > 18\
|deg| in Galactic coordinates.  The DECam Legacy Survey is providing these data
in the equatorial region at |delta| < +30\ |deg| using the Dark Energy Camera on
the Blanco Telescope.

Data Release 1 (DR1) is the first public data release of images and catalogs for
the DECam Legacy Survey.  It includes DECam data primarily from z-band
observations in August 2013 (https://legacy.noirlab.edu/perl/abstract?2013A-0741) and
g,r,z-band observations from August 2014 through January 2015 for an NOAO survey
program (https://legacy.noirlab.edu/perl/abstract?2014B-0404).  It also includes
public data from other programs near the Fall celestial equator bounded by 315 <
|alpha| < 360 |deg| or 0 < |alpha| < 5 |deg|, and by -3\ |deg| < |delta| < +3\
|deg|.  In total, the optical data covers a disjoint 3100 deg\ |sup2| footprint,
with 1200 deg\ |sup2| in g-band, 1300 deg\ |sup2| in r-band and 2700 deg\ |sup2|
in z-band, of which 750 deg\ |sup2| of this footprint has been observed in all
three optical filters.  Only 110 deg\ |sup2| of this footprint is covered to the
expected final depth of g=24.0, r=23.6, z=23.0.  Non-photometric data have been
excluded from this first data release.

There are approximately 140 million unique sources, of which approximately
750,000 have SDSS spectroscopy.

DR1 includes the stacked images and the Tractor-based catalogs.
The size of this data distribution is:

===== ========= ==============================================
Size* Directory Description
===== ========= ==============================================
238GB tractor   Tractor catalogs
15TB  coadd     Co-added images, including |chi|\ |sup2|, depth, image, model, nexp, and PNG quality-assurance plots
41GB  sweep     Light-weight versions of the Tractor catalogs
===== ========= ==============================================

\*Note that although the *contents* of a directory should be fixed for each Data Release, the *size* of a directory can change. This is typically due to updated file compression. So, the listed directory sizes should be viewed as (very reasonable) estimates.

The co-added images and Tractor catalogs are presented in bricks of approximate
size 0.25\ |deg| |times| 0.25\ |deg|.  These images are identical projections
for each of the g,r,z filters.

Source Detection
================

The source detection relies upon a combination of known SDSS sources
and a PSF-matched-filter detection of the DECam stacked images.
This should include all detections of sources to near the 5\ |sigma|
detection limit.  The Tractor fitting step is initialized with
these positions and classifications, although those positions and
classifications can be changed during the fits and low-S/N sources
are removed.

The existing SDSS DR12 sources are included with the RA,DEC coordinates,
classifications, and shape parameters from that catalog.

Each DECam image is convolved by its PSF model, then a weighted stack
of these is created in order to optimize the point-source detection
efficiency.  Next, SED-matched combinations of the three bands are
created, for two SEDs: "flat" (a source with AB color zero), and
"red", a source with AB color :math:`g-r = 1`, :math:`r-z = 1`.  Sources above 5\ |sigma|
are detected in each of these two SED-matched filters.  Sources (blobs
of significant pixels) containing an SDSS catalog object are removed.
Remaining sources are added as point sources in the Tractor fitting.

PSF
===

The Tractor makes use of the PSF on each individual exposure.  There is no
PSF computed for the image stacks, as that would not be used.

The PSF for the individual exposures are first computed independently for each CCD
using PSFEx, generating pixelized models.  Those PSFs are then re-fit by a spatially-varying mixture of gaussians (MoGs).


Sky Level
=========

The Community Pipeline removes a sky level that includes a sky pattern, an illumination correction,
and a single scaled fringe pattern.  These steps are described here:
https://legacy.noirlab.edu/noao/staff/fvaldes/CPDocPrelim/PL201_3.html .
This makes the sky level in the processed images near zero, and removes most pattern artifacts.
A constant sky level is then added back to the image that is mean of what was removed.

The Tractor removes a constant sky computed from the median on the object residual image.
This value can be found as the SKY_P0 keyword in the calibration files cosmo/work/decam/calib/sky.
The stacked images have this sky level removed.

Tractor Catalogs
================

The Tractor code runs within the geometrical region
of a brick.  This fitting is performed on the individual exposures
that overlap the brick, without making use of the image stacks.
This preserves the full information content of the data set in the fits,
handles masked pixels without the need for uncertain interpolation techniques,
and fits to data points within the complication of pixel covariances.

To improve the run time for Tractor, each brick is divided into blobs
that are collections of contiguous pixels above a detection level and
the neighboring pixels.  The boundaries of the blobs are made from
the stacked detection image.  For the high-Galactic latitudes of the
DR1, all bricks can be successfully divided into blobs that contain
few enough sources to run in a reasonable amount of time.
This approach is likely to fail near the Galactic plane, where the
high density of stars would be expected to result in a brick appearing
as a single blob.

Morphological classification
============================

The Tractor fitting can allow any of the source properties or
image calibration parameters (such as the PSF) to float.
Only the source properties were allowed to float in this run.
These are continuous properties for the object centers, fluxes,
and the shape parameters.  The discontinous properties are
the choice for each source model: point source, exponential,
deVaucouleurs, or a composite exponential+deVauc.  In this run, the
initialization of sources uses SDSS models where available or otherwise
a point source.

Four morphological types are used: point sources, deVauc profiles
(elliptical galaxies), exponential profiles (spiral galaxies), and composite
profiles that are deVauc + exponential (with the same source center).
The decision to retain an object in the catalog and to re-classify as
models more complicated than a point source are made using the penalized
changes to |chi|\ |sup2| in the image after subtracting the models for
other sources.
A source is retained if this penalized |chi|\ |sup2| is improved by 25;
this corresponds to a |chi|\ |sup2| difference of 27 (because of the penalty
of 2 for the source centroid).  Sources below this threshold are removed.
The classification is as a point source unless the penalized |chi|\ |sup2|
is improved by 9 (*i.e.*, approximately a 3\ |sigma| improvement) by treating
it as a deVauc or exponential profile.
The classification is a composite of deVauc + exponential if it both a
better fit to a single profile over the point source, and the composite improves
the penalized |chi|\ |sup2| by another 9.  These choices implicitly mean
that any extended source classifications have to be at least 5.8\ |sigma| detections
and that composite profiles must be at least 6.5\ |sigma| detections.

The fluxes are not constrained to be positive-valued.  This allows
the fitting of very low signal-to-noise sources without introducing
biases at the faint end.  It also allows the stacking of fluxes
at the catalog level.


Tractor Implementation Details
==============================

Tractor fundamentally treats the fitting as a |chi|\ |sup2| minimization
problem.  The current core routine uses the sparse least squares
solver from the scipy (scientific python) package, or the open source
Ceres solver (http://ceres-solver.org), originally developed by
Google.

The PSF models and the PSF-convolved galaxy profiles are approximated
with mixture-of-gaussian (MoG) models (http://arxiv.org/abs/1210.6563).
This is not an exact representation, but introduces errors in these
models typically at the level of :math:`10^{-4}` or smaller.
The MoGs are treated as the pixel-convolved quantities for the PSF, etc,
and are evaluated at the integral pixel coordinates without integrating
any functions over the pixels.

The Tractor algorithm could be run with both the source parameters
and the calibration parameters allowed to float, at the cost of
more compute time and the necessity to use much larger blobs because
of the non-locality of the calibrations.  A more practical approach
would be to iterate between fitting source parameters in brick space,
and fitting calibration parameters in exposure space.  Such iterations
will be considered and tested for future data releases.
Another practical issue is that the current PSF models may allow
too much freedom.


Photometry
==========

The flux calibration for the DR1 is on the AB natural system of the DECam instrument.
An AB system reports the same flux in any band for a source whose spectrum is
constant in units of erg/s/cm\ |sup2|/Hz. A source with a spectrum of
:math:`f = 10^{-(48.6+22.5)/2.5}` erg/s/cm\ |sup2|/Hz
would be reported to have an integrated flux of 1 nanomaggie in any filter.
The natural system means that we have not
applied color terms to any of the photometry, but report fluxes as observed in the DECam filters.

Zero point magnitudes for the CP version 2 reductions of the DECam images
were computed by comparing 7\ |Prime| diameter aperture photometry to PS1
photometry, where the latter was modified with color terms
to place the PS1 photometry on the DECam system.  The same color terms
are applied to all CCDs.
Zero points are computed separately for each CCD, but not for each amplifier.
The color terms to convert from PS1 to DECam were computed for stars
in the color range :math:`0.4 < (g-i) < 2.7` as follows:

.. math::
                (g-i) & = & g_{\mathrm{PS}} - i_{\mathrm{PS}} \\
   g_{\mathrm{DECam}} & = & g_{\mathrm{PS}} + 0.04709 (g-i) + 0.00084 (g-i)^2 - 0.00340 (g-i)^3 \\
   r_{\mathrm{DECam}} & = & r_{\mathrm{PS}} - 0.09939 (g-i) + 0.04509 (g-i)^2 - 0.01488 (g-i)^3 \\
   z_{\mathrm{DECam}} & = & z_{\mathrm{PS}} - 0.13404 (g-i) + 0.06591 (g-i)^2 - 0.01695 (g-i)^3 \\

The brightness of objects are all stored as linear fluxes in units of nanomaggies.  The conversion
from linear fluxes to magnitudes is as follows:
:math:`m = 22.5 - 2.5 \log_{10}(\mathrm{flux})`
These linear fluxes are well-defined even at the faint end, and the errors on the linear fluxes should
be very close to a normal distribution.  The fluxes can be negative for faint objects, and indeed we
expect many such cases for the faintest objects.

The SDSS, DECam and WISE fluxes are all within a few percent of being on an AB system.
The WISE Level 1 images and the unWISE image stacks are on a Vega system.
We have converted these to an AB system using the recommended conversions by
the WISE team documented here
http://wise2.ipac.caltech.edu/docs/release/allsky/expsup/sec4_4h.html#conv2ab
:math:`\mathrm{Flux}_{\mathrm{AB}} = \mathrm{Flux}_{\mathrm{Vega}} * 10^{-(\Delta m/2.5)}`
where :math:`\Delta m` = 2.699, 3.339, 5.174, and 6.620 mag in the W1, W2, W3 and W4 bands.
For example, a WISE W1 image should be multiplied by :math:`10^{-2.699/2.5} = 0.083253` to
give units consistent with the Tractor catalogs.

.. _`DR8 catalogs`: ../../dr8/catalogs/#galactic-extinction-coefficients

Galactic Extinction
===================

The most recent values of the Galactic extinction coefficients are available on the `DR8 catalogs`_ page.


Astrometry
==========

The astrometry is currently tied to star positions in Pan-STARRS-1,
which is implicitly at the time of observation for Pan-STARRS-1.

The Astrometry.net code has been run on SourceExtractor-generated source lists
(the same sources used for PSF determination).  This yields WCS headers with
2nd-order SIP polynomial distortions.  The astrometric reference catalog
is from Pan-STARRS-1.  This is solved independently on each CCD.

In the DR1 footprint, the SDSS imaging data spans 1998 through 2005,
and the SDSS spectroscopic data spans Feb 2000 through April 2014.

Comparison of the astrometric zero point for each image to the PS1 star positions shows systematic
differences for individual CCDs in the image. The residuals are shown by the arrows in the attachments below
(Offsets*ps*gz). The systematic residuals are typically smaller than |plusmn|\ 0.03\ |Prime|.

In the future, the plan is to tied the astrometry to the GAIA astrometry,
at which point we will use the predicted stellar positions at the
DECam epoch of observation.

Image Stacks
============

The image stacks are provided for convenience, but were not used in the Tractor fits.
These images are oversized by approximately 260 pixels in each dimension.
These are tangent projections centered at each brick center, North up, with dimensions of 3600 |times| 3600
and a scale of 0.262\ |Prime|/pix.


Depths
======

The median 5\ |sigma| point source depths for areas in the DR1 with 3 observations is g=24.65, r=23.61, z=22.84.
This is based upon the formal errors in the Tractor catalogs for point sources; those errors need more confirmation.
This can be compared to the depths in the proposal for 2 observations at 1.5\ |Prime| seeing predicting g=24.7, r=23.9, z=23.0.


Code Versions
=============

* NOAO Community Pipeline
* Sextractor, PSFEx
* Astrometry.net
* Tractor


Glossary
========

Astrometry.net
    `Dustin Lang's astrometry code <https://github.com/dstndstn/astrometry.net>`_.

Blob
    Continguous region of pixels above a detection threshold and neighboring
    pixels; Tractor is optimized within blobs.

Brick
    A region bounded by lines of constant RA and DEC; the DR1 reductions
    are performed within bricks of size approximately 0.25\ |deg| |times| 0.25\ |deg|.

CP
    Community Pipeline (DECam reduction pipeline operated by NOAO;
    https://legacy.noirlab.edu/noao/staff/fvaldes/CPDocPrelim/PL201_3.html).

DECaLS
    `Dark Energy Camera Legacy Survey <http://legacysurvey.org>`_.

DR1
    DECam Legacy Survey Data Release 1, May 2015.

DECam
    Dark Energy Camera on the NOAO Blanco 4-meter telescope.

maggie
    Linear flux units, where an object with an AB magnitude of 0 has a
    flux of 1.0 maggie.

MoG
    Mixture-of-gaussian model to approximate the PSF and galaxy models (http://arxiv.org/abs/1210.6563).

NOAO
    `National Optical Astronomy Observatory <https://legacy.noirlab.edu>`_.

nanomaggie
    Linear flux units, where an object with an AB magnitude of 22.5 has a flux
    of :math:`1 \times 10^{-9}` maggie or 1.0 nanomaggie.

PSF
    Point spread function.

PSFEx
    `Emmanuel Bertin's PSF fitting code <http://www.astromatic.net/software/psfex>`_.

SDSS
    `Sloan Digital Sky Survey <http://www.sdss.org>`_.

SDSS DR12
    `Sloan Digital Sky Survey Data Release 12 <https://www.sdss.org/dr12/>`_.

SED
    Spectral energy distribution.

SExtractor
    `Source Extractor reduction code <http://www.astromatic.net/software/sextractor>`_.

SFD98
    Schlegel, Finkbeiner & Davis 1998 extinction maps (https://ui.adsabs.harvard.edu/abs/1998ApJ...500..525S/abstract).

Tractor
    `Dustin Lang's inference code <https://github.com/dstndstn/tractor>`_.

unWISE
    New coadds of the WISE imaging, at original full resolution
    (http://unwise.me, http://arxiv.org/abs/1405.0308).

WISE
    `Wide Infrared Survey Explorer <http://wise.ssl.berkeley.edu>`_.
