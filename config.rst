The Config File
===============

Many parts of the `pytpc` code are configured using settings from an external config
file that is read at the beginning of the fitting or simulation process. All of the scripts
for fitting and simulation that are provided with them`pytpc` package share the same
config file, making it simple to keep the simulations and the experimental data in sync.

The config file is formatted as YAML_, a lightweight data serialization language. This
format is parsed into a Python dictionary when the file is read. An annotated example file
is presented below.

..  _YAML: https://en.wikipedia.org/wiki/YAML

Parsing the config file
-----------------------

The configuration file can be read using the PyYAML library. This can be installed with :command:`pip`:

..  code-block:: shell

    pip install pyyaml

Then, the file can be read as follows:

..  code-block:: python

    import yaml

    with open('/path/to/config.yml', 'r') as f:
        config = yaml.load(f)


The configuration parameters will then be available in the standard Python dictionary object ``config``.

.. _configPatchingSection:

Config patching
---------------

Sometimes certain parameters need to be overridden for different conditions. This might happen if there
were a number of different sets of conditions during an experiment, or if some of the files identified in
the config file are located at different paths on different computers. This can be accomplished by writing
additional small YAML files with the keys you want to override, and then applying them as patches on the
main config file after loading it. This is done with the Python :meth:`dict.update` method.

For example, say that some runs were taken without a tilt angle. This could be accounted for with this
configuration patch:

..  code-block:: YAML

    ---
    # patch.yml

    tilt: 0

This can then be read after the main config file and used to update the configuration:

..  code-block:: python

    import yaml

    with open('/path/to/main_config.yml', 'r') as f:
        config = yaml.load(f)

    with open('/path/to/config_patch.yml', 'r') as f:
        patch = yaml.load(f)

    config.update(patch)

The key ``'tilt'`` in the dictionary ``config`` will be updated with the new value of 0, and the rest of the
keys will be unchanged.

Sample file
-----------

..  code-block:: YAML

    ---
    # =======================
    # Paths to required files
    # =======================

    # Path to the lookup table that maps position to pad number
    lut_path: /mnt/home/jbradt/Documents/Code/ar40-aug15/monte_carlo/LUT.h5

    # Path to the directory that contains the GET electronics config files
    xcfg_root: /mnt/home/jbradt/Documents/Data/e15503b/configs

    # Sub-path in ``xcfg_root`` where the desired config file lives
    xcfg_path: run_0077/configure-e15503b.xcfg

    # Path to a CSV-formatted lookup table with columns (cobo, asad, aget, channel, pad number)
    padmap_path: /mnt/home/jbradt/Documents/Data/e15503b/Lookup20150611.csv

    # Path to a list of extra pads to exclude when calibrating, perhaps pads from the beam region
    beampads_path: /mnt/home/jbradt/Documents/Code/ar40-aug15/notebooks/beampads_e15503b.txt

    # Path to a pedestals file in CSV format with columns (pad number, pedestal value)
    pedestals_path: /mnt/home/jbradt/Documents/Data/e15503b/Pedestals20150810.csv

    # ===================
    # Detector conditions
    # ===================

    # The detector tilt angle, in degrees
    tilt: 6.2

    # The pad plane rotation angle, in degrees
    pad_rot_angle: -108

    # The electron drift velocity in cm/us, in the detector (xyz) coordinate system
    vd: [-0.0603573, -0.55178666, -5.14005679]

    # The electric field in V/m, in the beam (uvw) coordinate system
    efield: [0., -1025.993, 9444.434]

    # The magnetic field, in Tesla, in the beam (uvw) coordinate system
    bfield: [ 0.  ,  0.  ,  1.68]

    # The name of the gas in the detector. This must be the name of a gas that is
    # defined in the gas database.
    gas_name: 'isobutane'

    # The gas pressure in the active volume, in torr
    gas_pressure: 19.2

    # The ionization energy used when converting energy deposited in the gas into a
    # number of electrons, given in eV.
    ioniz: 23.0

    # Standard deviation of the electron diffusion distribution, in m
    diffusion_sigma: 0.1e-3

    # Standard deviation of the electron diffusion distribution (in m) to be used when
    # simulating events. This might be smaller than the value used when fitting.
    evtgen_diffusion_sigma: 0.1e-4

    # ======================
    # Electronics parameters
    # ======================

    # This section establishes parameters of the GET electronics. Many of these values can
    # be found in the GET xcfg files for an experimental run.

    # The CoBo write clock frequency, in MHz
    clock: 12.5

    # The shaping time, in s
    shape: 280e-9

    # The pad trigger threshold. The most-significant bits (MSB) are listed in the xcfg
    # file under the key "GlobalThresholdValue", and the least-significant bits are
    # listed at the channel level under the key "LSBThresholdValue". Both of these are
    # decimal integers.
    pad_thresh_MSB: 1
    pad_thresh_LSB: 2

    # The width of the trigger signal pulse, in seconds. This is controlled by the
    # keys "isTOTActive" and "isTriggerWidth200ns" in Reg1. See the AGET documentation for
    # details about how this value is determined.
    trigger_signal_width: 235e-9

    # The CoBo multiplicity threshold in the same arbitrary units as the xcfg file.
    multiplicity_threshold: 20000

    # The CoBo multiplicity window. This is given by the key "multWindowSize" in the xcfg
    # file, and the units are the number of periods of the 25 MHz clock.
    multiplicity_window: 300

    # The gain set for the GET electronics, in coulombs.
    electronics_gain: 120e-15

    # The fraction of the full ADC range covered by the discriminator. This should be given
    # as a fraction like `0.175`, not a percentage.
    trigger_discriminator_fraction: 0.175

    # The gain of the micromegas.
    micromegas_gain: 500

    # ===================
    # Particle parameters
    # ===================

    # This section establishes the properties of the particles being tracked.

    # Mass number A, charge number Z, and initial energy per nucleon of the beam particle. The
    # energy should be given in MeV/u.
    beam_mass: 46
    beam_charge: 18
    beam_enu0: 4.17

    # Mass number A and charge number Z of the scattered particle.
    mass_num: 1
    charge_num: 1

    # The maximum total kinetic energy for the outgoing particle. This cutoff is used when creating a
    # lookup table for the energy loss data to be used in the C++ portion of the program. This
    # threshold must be an integer, and it should be given in MeV.
    tracker_max_en: 100

    # ==================
    # Fitting parameters
    # ==================

    # Time bucket corresponding to micromegas, from trigger setup. This is used in the z calibration.
    micromegas_tb: 38

    # Number of Monte Carlo iterations to run for each event.
    num_iters: 20

    # Number of parameter sets to simulate in each iteration.
    num_pts: 500

    # Multiplicative factor by which the parameter space is compressed after each iteration.
    red_factor: 0.8

    # Initial size of the parameter space.
    sigma:
        x: 0.1    # Vertex x position, in m
        y: 0.1    # Vertex y position, in m
        z: 0.1    # Vertex z position, in m
        enu: 4.0  # Initial energy per nucleon of outgoing particle, in MeV/u
        azi: 60   # Azimuthal angle of scattering, in degrees
        pol: 30   # Polar angle of scattering, in degrees

    # ===================
    # Cleaning parameters
    # ===================

    # This key contains all of the configuration parameters for the Hough space cleaner.
    cleaning_config:
        # The largest radial distance to consider in the linear Hough space.
        linear_hough_max: 2000

        # The number of bins to use in the linear Hough space.
        linear_hough_nbins: 500

        # The largest radial distance to consider in the circular Hough space.
        circle_hough_max: 500

        # The number of bins to use in the circular Hough space.
        circle_hough_nbins: 200

        # The number of bins to consider in each direction when finding the center of mass of
        # peaks in the Hough space.
        peak_width: 4

        # The maximum orthogonal distance, in mm, that a point may be from the nearest line without
        # being classified as noise.
        max_distance_from_line: 40

        # The minimum number of points that constitutes a valid line.
        min_pts_per_line: 10

        # The minimum number of neighbors that a point must have to be valid.
        min_num_neighbors: 1

        # The radius, in mm, of the neighborhood used for the nearest-neighbor cut.
        neighbor_radius: 15

        # Any time bucket after this will be dropped
        last_tb: 505

    # =================
    # VME channel setup
    # =================

    # This lists the names identifying the channels in the VME data in order of increasing
    # channel number. The ADC channel labels are used when unpacking the VME data files into
    # HDF5 files, and the scaler labels are used for convenience when working with the
    # scaler data.
    vme_channels:
        adc:
          - mesh
          - ic
          - trig
        scalers:
          - cobo0
          - cobo1
          - cobo2
          - cobo3
          - cobo4
          - cobo5
          - cobo6
          - cobo7
          - cobo8
          - cobo9
          - cobo_or
          - busy_or
          - mesh
          - beam
          - ic_downscale
          - ic
          - free_trig
          - live_trig

    # =====================
    # Simulation parameters
    # =====================

    # The number of events to simulate in the simulation. This can be overridden with
    # a command line argument.
    dist_num_pts: 5000

    # Standard deviation of the distribution of Gaussian noise added to each simulated
    # signal, given as a number of ADC bins.
    noise_stddev: 6.0

    # Scaling factor for the simulated baseline depression from the beam. The amplitude of
    # the mesh signal of the simulated beam track is multiplied by -1 and divided by
    # this factor before being added to each signal.
    baseline_depression_scale: 40.0

    # The baseline depression is this many times larger in the big pads.
    big_pad_multiplier: 4

    # Parameters for the simulated beam dispersion. The beam angle is simulated over a
    # uniform distribution of angles with the maximum angle given below. The origin
    # in z sets the location of the focus point of this angular distribution
    # as a distance from the micromegas, in m. Therefore, this focus should be >= 1.0.
    max_beam_angle: 2    # degrees
    beam_origin_z: 1.14  # m

    # =============
    # Logging setup
    # =============

    # These values are used to configure Python's builtin logging library. See the documentation
    # for that library for details.
    logging_config:
        version: 1
        disable_existing_loggers: false
        formatters:
            simple:
                format: '[%(name)s.%(funcName)s] %(levelname)s: %(message)s'
        handlers:
            console:
                class: 'logging.StreamHandler'
                formatter: 'simple'
                level: 'INFO'
        root:
            level: 'INFO'
            handlers:
                - console
