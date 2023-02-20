
# OVERVIEW
This SEDNA configuration has been developped within the ImMEDIAT project framework (French ANR funding). The project main purpose is to understand the role of ocean and sea ice small scale features in the Arctic. 
The SEDNA-DELTA experiment relies on the [NEMO](https://www.nemo-ocean.eu) numerical plateform; it uses both [NEMO-OCE](https://doi.org/10.5281/zenodo.1464816) and [NEMO-SI3](https://doi.org/10.5281/zenodo.1471689) models for the ocean and the sea ice components respectively. It aims at simulating the ocean/sea-ice dynamics over the north Atlantic ocean (from ~54°N), the Greenland-Iceland-Norway seas and the whole Arctic basin with a limit along the Bering strait. The SEDNA-DELTA experiment required more than 35 Mhours of computing ressources attributed after a Peer Review Process by the [PRACE](https://prace-ri.eu) consortium (funding from the EU’s Horizon 2020 Research and Innovation Programme) for one year. The run has been performed on the Joliot-Curie HPC computer hosted by the [TGCC](https://www-hpc.cea.fr/en/TGCC.html).<br>
* Main technical aspects hereafter:  
** The horizontal grid is a 1/60 degree i.e. from ~1km at the southern limit down to ~800m in most of the Arctic basin. The grid domain has more than 6 billion grid points.<br>
** A 150 vertical levels from 1m close to the surface up to 100m at the ocean bottom.<br>
** The model time step is ∆t=40s. <br>
** The SEDNA-DELTA experiment covers the range 2009-2015 and has been forced with the [ERA5](10.24381/cds.adbb2d47) data set.<br>
** A total of 17404 CPUs have been mobilised for the simulation with respectively 16860 CPUs for NEMO and 544 CPUs dedicated to XIOS servers.<br>


<p align="center"> <img src="./figs/SEDNA_Xif_z181m.jpg" width="700" height="600"> </p>
<p align = "center">
Early March daily mean relative vorticity over Coriolis in the Beaufort Gyre at ~181m depth in SEDNA
</p>

## SEDNA-DELTA <br>
This reprository hold : <br>
  1. the NEMO code source version used (how to download it) as the specific code changes for SEDNA as well 
  2. the physics and numerics setup for both ocean and sea ice components in the namelists <br>
  3. an exhaustive list of other input files among which: the horizontal/vertical grid, the bathymetry and the surface forcing used to perform the numerical experiment called SEDNA-DELTA. <br>

### SOURCE CODE : 
   * The ocean/sea ice model: <br>
	** The code source relies on the official NEMO release 4.0.5. Information can be found [there](https://forge.ipsl.jussieu.fr/nemo/chrome/site/doc/NEMO/guide/html/guide.html).<br>
	** The specific code source changes associated to this experiment are in the [MY_SRC](./NEMOGCM/cfgs/SEDNA-DELTA/MY_SRC) directory. The [arch](./NEMOGCM/arch) list the computing architecture file used to compile this configuration on the TGCC Rome partition.
   * The XIOS libray to perform outputs:<br>
	** Outputs have been realised in using the XIOS 2.5 library; the revision 1920 has been downloaded and compiled. Few hard coded modifications were required to be able to run this configuration. More information there [XIOS](https://forge.ipsl.jussieu.fr/nemo/chrome/site/doc/NEMO/guide/html/install.html#extract-and-install-xios).

### INPUT FILES :
1 - Time invariant input files:
   * Domain configuration: <br>
	** Description: This domain file gathers the horizontal & vertical grids, the bathymetry relies on [ETOPO1](http://dx.doi.org/10.7289/V5C8276M) data set as the grid points geographical location as well of the configuration. This new 1/60 grid has been built from a global ORCA 1/60 that has been built starting from a global ORCA 1/12 grid.<br>
	** File name: ```SEDNA_Domain_cfg_Tgt_20210820_tsh10m_L1.nc```<br>
   * Ocean initialisation: <br>
	** Description: [World Ocean Atlas 2009](https://accession.nodc.noaa.gov/0094866) at 1°x1° climatology <br>
	** File name:```woa09_temperature_monthly_1deg_t_an_CMA_drowned.nc, woa09_salinity_monthly_1deg_s_an_CMA_drowned.nc``` <br>
   * Ice initialisation:<br>
	** Description: PIOMAS re-analysis data from January 2009 [Data server](https://pscfiles.apl.washington.edu/zhang/PIOMAS/data/v2.1/heff_txt/) [DOI](https://doi.org/10.1175/1520-0493(2003)131%3C0845:MGSIWA%3E2.0.CO;2)<br>
	** File name:```PIOMAS-SEDNA_<var>_20210825_Tgt_Z_y2009.nc``` <var> stands for either  heff, snow, or area <br>
   * Tidal mixing parametrization: <br>
	** Description: A new comprehensive parameterization of mixing by breaking internal tides and lee waves (de Lavergne et al., 2019) <br>
	** File name:```mixing_power_<var>_REG05-SEDNA_Tgt_tsh_20210823.nc``` <var> stands for bot, sho cri or nsq and  ```decay_<dvar>_REG05-SEDNA_Tgt_20210823.nc ``` <dvar> stands for either bot or cri <br>
   * Distance to the coast:<br>
	** Description: gives the distance to the coast of each ocean grid points. It is used to switch off the SSS restoring in a 150km wide range from the coast.<br>
	** File name: ```SEDNA_dist.coast_20210823_tsh10m_L1.nc```<br>

2 - Time varying input files:
   * Surface forcing:<br>
	** Description: The atmospheric forcing data set [ERA5](10.24381/cds.adbb2d47), with ```CORE bulk formulae``` (from NCAR) and relative winds (ocean surface velocity components taken into account for the wind stress<br>
	** File name: ```ERA5_<var>_drwnd.nc``` <var> stands for either u10,v10,msdwswrf,msdwlwrf,mtpr,msr,msl,t2,q2.<br>
	** Frequency: hourly for all fields <br>
   * Rivers and Greenland melting fluxes: <br>
	** Description: A blend of data from Dai & Trenberth for rivers volume fluxes and Bamber for fresh water resulting from Greenland melting<br>
	** File name: ```SEDNA_runoff_monthly_combined_Dai_Trenberth_Bamber_Z.nc``<br>
	** Frequency: monthly<br>
   * Open boundaries:<br>
	** Description: 3 open boundaries conditions limit the regional domain, about 45°N in the North Atlantic, along the Bering strait and at the Baltic sea output. Daily data from the CMEMS [GLORYS12V1](https://doi.org/10.48670/moi-00021) re-analysis have been used.<br>
	** File name: ```GLORYS12V1-SEDNA_ATLSPG.1d_<var>.nc``` and ```GLORYS12V1-SEDNA_BERING.1d_<var>.nc```. <var> stands for grid2D, U, V, T, S and icemod for the Bering open boundary<br>
	** Frequency: daily <br>
   * [World Ocean Atlas 2009](https://accession.nodc.noaa.gov/0094866) surface salinity:<br>
	** Description: Sea surface salinity restoring with a piston velocity of 167 mm/day ( 60 days/10 meters )<br>
	** File name: ```woa09_sss01-12_monthly_1deg_s_an_CMA_drowned.nc``` and ```woa09_sst01-12_monthly_1deg_t_an_CMA_drowned.nc```<br>
	** Frequency: monthly<br>
   * [World Ocean Atlas 2009](https://accession.nodc.noaa.gov/0094866) surface temperature:<br>
	** Description: to avoid SSS restoring where the SST climatology temperature is at the freezing point, i.e. in presence of sea-ice<br>
	** File name: ```woa09_sst01-12_monthly_1deg_t_an_CMA_drowned.nc```<br>
	** Frequency: monthly<br>
