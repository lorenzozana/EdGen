EdGen Event Generator
==================

This code is for a common Event Generator for the HASPECT collaboration.
It uses the ROOT (root.cern.ch) PhaseSpace generator has basis and 
See http://www2.ph.ed.ac.uk/~lzana/Documents/zana_haspect_genova2014.pdf for a quick presentation on this code.
This version respect to the common version it supports the BOS output format (for CLAS6 analysis) if the system has it.

Prerequisites
-------------
* CERN ROOT  (tested at now with version ROOT 5.34.36 and 6.10.00 (seems there are problem with earlier version of root)
* git 
* cmake
* Tested on ifarm.jlab.org on June 5 2014. Needs CLAS software (and OLD CERN LIBS) 
* Environment variables on ifarm.jlab.org

.

source ./environment.csh (own version of environment variables for setting CLAS software in ifarm. It uses the latest version of root 5.34.34). This is not needed: 
1) OUTSIDE THE JLAB FARM 
2) IN THE JLAB FARM IF YOU DON'T NEED BOS SUPPORT (you are using gemc, for example)

Install
-------

* cd EdGen (go to the EdGen directory)
* mkdir build
* cd build
* if you want to install BOS output support you will need to have correctly setup in your environment variables CLAS6LIB CLAS6INC and CERNLIB (the environment show before has been tested)
* if you don't want BOS support, just unset those environment variables (CLAS6LIB CLAS6INC and CERNLIB). If you will request a BOS output the code will not create any bos file (strangely enough)
* cmake ../ 
* make


Running
-------
* cd EdGen/output (go to the output directory)
* A table with particle properties is in this directory (from PDG) eg_pdg_table.txt :Modify it if you need it. An example of how to add particles to this table are written at the end of the file. At now the information used are just the mass, the charge of the particle and the lifetime (if the particle has vertex in your reaction diagram for this MonteCarlo. 
* A template is in input.dat (input_test.dat it is just for my developments at now), and modify the file to fit your reaction
* In input.dat one can specify the input spetrum (for example for CLAS photon beam). The input spectrum file is written in a txt files format with raw that represent for each bin E_min, E_max, Counts (Does not need to be normalize, the code is going to normalize it if need it). The bin size do not need to be the same for each bin.  An example is written in the output directory as energy.txt. In the same way one can write the spectrum from Q2, nu, t (option model = 5).
* If you see a strong memory consumption, or you just need to speed up this generator (for example you have a strong angle cut on the electron or you have many particles (>4) from a single vertex) , try to run it with option wmod: 1 . NB you will need to carry the weight if some of your vertex has more than 2 particles generated from it (In the LUND or BOS file, the weight will be a single number as the multiplication of the weight of each single vertex of each event). The other option will be using wmod: 2 and set the weight of each vertex to the maximum calculated (plus some extra to avoid cutting some low statistic simulation ). In order to find which value is the correct one for each vertex, just run with option wmod: 1 (weight will be generated and recorded) and plot the weight at each vertex. 
* ./EdGen -h will give you the options
* <b>./EdGen -i input.dat -o output.root        (Change input.dat or output.root (need to be a *.root) to your desired input and output file name) </b> 
  
Input file
----------
* nevt:    20000;                  NUMBER OF EVENTS TO GENERATE
* nprint:  1000;                   NUMBER OF EVENTS TO HAVE A PRINTOUT (FOR DEBUGGING)
* model:   2;      		 MODELS AVAILABLE (SEE BOTTOM FOR DIFFERENT OPTIONS)
* M_mode:  1;          MASS MODELS AVAILABLE (SEE BOTTOM FOR DIFFERENT OPTIONS)
* wmod:    0;          WEIGHT FOR EVENTS WILL BE KEPT (wmod= 1); A SECOND RUNDOM GENERATION WILL BE DONE TO REMOVE THE WEIGHT (wmod = 0 (SLOWER AND DEFAULT) );   A SECOND RUNDOM GENERATION WILL BE DONE TO REMOVE THE WEIGHT AND THE MAXIMUM OF THE WEIGHT AT EACH VERTEX WILL BE DECIDED IN THE FLAG vtype (wmod = 2 (FASTER) ) 
* ifile:	 energy.txt; 		 INPUT FILE SPECTRUM FOR BEAM (NEEDED FOR OPTION MODEL = 2) 
* qfile:      q2profile.txt;             INPUT FILE SPECTRUM FOR Q2 (NEEDED FOR OPTION MODEL = 5)
* tfile:       tprofile.txt;             INPUT FILE SPECTRUM FOR t (NEEDED FOR OPTION MODEL = 5)
* efile:       eprofile.txt;             INPUT FILE SPECTRUM FOR E' (energy scattered beam) (NEEDED FOR OPTION MODEL = 5)
* beam:    22;			 BEAM PARTICLE ID
* en:	 11.0    GeV;		 BEAM ENERGY (NEEDED FOR OPTION MODEL = 1)
* Erange:  2.0,5.0 GeV;          ENERGY RANGE FOR BEAM FLAT BETWEEN THESE 2 VALUES (NEEDED FOR OPTION MODEL = 3)
* tg_Z:    1;	 		 TARGET Z (NOT WORKING YET)
* tg_N:    1;			 TARGET N (NOT WORKING YET)
* tg_mass  1.876  GeV;           TARGET MASS
* length:	 40	cm;		 LENGTH TARGET
* ras_x:	 0.2	cm;		 BEAM PROFILE (GAUSSIAN SIGMA IN THE X DIRECTION)
* ras_y:	 0.2	cm;		 BEAM PROFILE (GAUSSIAN SIGMA IN THE Y DIRECTION)
* offset:  0.12,0.14,1.1 cm;	 OFFSET TARGET (X,Y,Z)
* qffile: FermiDist.root hParis; #file containing quasi free momentum distribution (should be a TH1 in MeV)
*  	  		 	 #second argument should be the name of the TH1 (hArgonne, hParis, hFlat)
* qfpdg: 2112,2212;		 #pdg id of quasi free target and spectator (total target mass=tg_mass> qf target + spectator)
*  	 			 #Note if nuclei larger than deuteron you will need to define a new spectator PDG in ed_pdg_table.txt 
* npart:   5;	       		 NUMBER OF PARTICLE INVOLVED IN THE INTERACTION (EXCLUDING BEAM AND TARGET)
* pid:     11,2212,113,211,-211;	 PARTICLE ID OF THE PARTICLE SPECIFIED WITH npart
* theta_min:   2.5,4.0,5.0,4.0,5.0 deg;		 THETA CUT FOR SINGLE PARTICLE (FROM 'pid:' flag) (AT NOW IS AN HARD CUT ON THE SIMULATED DATA)
* theta_max:   180.0,180.0,180.0,180.0,180.0 deg;		 THETA CUT FOR SINGLE PARTICLE (FROM 'pid:' flag) (AT NOW IS AN HARD CUT ON THE SIMULATED DATA)
* energy_min:   0.0,0.0,0.0,0.0,0.0 GeV;            ENERGY CUT FOR SINGLE PARTICLE (FROM 'pid:' flag) (AT NOW IS AN HARD CUT ON THE SIMULATED DATA)             
* energy_max:   1.0,11.0,11.0,11.0,11.0 GeV;        ENERGY CUT FOR SINGLE PARTICLE (FROM 'pid:' flag) (AT NOW IS AN HARD CUT ON THE SIMULATED DATA)
* nvertex: 2;			 NUMBER OF VERTEXES IN THE INTERACTION
* vertex:  0,3;			 ORIGIN OF THE VERTEX (0 STANDS FOR BEAM+TARGET; IN THIS EXAMPLE: 1 STANDS FOR 11, 2-->2212, 3-->113, 4-->211, 4-->-211), NUMBER OF PARTICLES GOING OUT OF VERTEX (READ IN SEQUENCE FROM THE pid ENTRY)
* v_type:  1,1.0;		 TYPE OF VERTEX (AT NOW JUST OPTION 1, IN THE FUTURE DIFFERENT CROSS SECTION CAN BE APPLIED AT EACH VERTEX). THE SECOND NUMBER REPRESENTS THE MAXIMUM OF THE WEIGHT THAT WILL BE KEPT AT EACH VERTEX (IMPORTANT FOR SPEED UP WITH VERTEX WITH MORE THAN 2 PARTICLES). THIS LAST FLAG IS TRIGGERED WITH OPTION (wmod: 2;) 
* vertex:  3,2;			 ORIGIN OF VERTEX (3 IN THIS CASE IS PARTICLE WITH pid=113), NUMBER OF PARTICLE GOING OUT OF VERTEX (STARTING FROM THE ONE LEFT FROM THE PREVIOUS VERTEX IN THE pid ENTRY: IN THIS EXAMPLE: 211,-211)
* v_type:  1,1.0;		 SAME AS BEFORE
* output:  2;			 OUTPUT FORMAT (SEE BELOW FOR OPTIONS)
* END

Models
-------
* 1 Phase Space Single Energy (for example e-)
* 2 Phase Space Energy Spectrum (for example gamma)
* 3 Phase Space Energy Spectrum (for example gamma) flat energy spectrum
* 4 Cross Section (sorry, not yet)
* 5 t,q2,nu distribution from spectrum
* 6 Amplitudes (sorry, not yet) 
* 7 Data Points (sorry, not yet)

Mass Models
-------
(Mass model: if width of particle>1MeV, one can generate the mass according to different distributions)
* 1 Breit-Wigner (Mass and Width are automatically read from output/eg_pdg_table.txt)
* 2 Flat (Generated flat in mass in the allowed range)
* 3 Just the mass at the center of the distribution
* 4 Gaussian and more to come


output
-------
* 1  ROOT only
* 2  ROOT + LUND
* 3  ROOT + BOS

Examples
-------
* 3 particles in a vertex, Dalitz plots are generate using the weight (for particles that decay with more than 2 particles in a vertex). The weight is an array of all the particles and describe the weight at creation (the weight is given to the decayed particles) <br />
** Create generated output file: ./EdGen -i input_test2.dat <br />
** Analyze the output (with TProof) of the generated file (files analysis.C , analysis.h, run_analysis.C): root run_analysis.C <br />
** NB The weight is given as a single number (the product of the weights at each vertex) in the BOS and LUND File: For the BOS file the weight is included in the MCHD bank (check that you bank is keeped in all the step of your analysis); For the LUND file format, the weight is included where should be the value of the mass in the LUND format (since it is not used by gemc). <br />
* 2 particles per vertex, but 3 vertex <br />
** Create generated output file: ./EdGen -i input_test.dat <br />
** Analyze the output (with TProof) of the generated file (files newAnalysis.C , newAnalysis.h, run_newAnalysis.C): root run_newAnalysis.C <br />
* Photon production phasespace Omega + pi+ + pi- <br />
** Create generated output file: ./EdGen -i input_test_5.dat <br />
** Analyze the output (with TProof) of the generated file (files analysis_5.C , analysis_5.h, run_analysis_5.C): root run_analysis_5.C <br />
* Photon production phasespace a2->Omega + pi+ + pi- <br />
** Create generated output file: ./EdGen -i input_test_6.dat <br />
** Analyze the output (with TProof) of the generated file (files analysis_6.C , analysis_6.h, run_analysis_6.C): root run_analysis_6.C <br />
* See other examples of input file with input.dat (default), etc.
* Example with wmod:2 and maximum weight defined at the first vertex with vtype: input_test_8.dat <br />
* Photon production phasespace (flat energy range)  Omega + pi+ + pi- <br />
** Create generated output file: ./EdGen -i input_test_7.dat <br />
** Analyze the output (with TProof) of the generated file (files analysis_7.C , analysis_7.h, run_analysis_7.C analysis_7_output.root): root run_analysis_7.C <br />
* Electron Production of rho with t,q2,nu as input <br />
** Create generated output file: ./EdGen -i input_t_test1.dat <br />
** Analyze the output (with TProof) of the generated file (files analysis_t_test1.C analysis_t_test1.h run_analysis_t_test1.C analysis_t_test1_output.root): root run_analysis_t_test1.C <br />
* Electron Production of  a2->Omega + pi+ + pi- with t,q2,nu as input <br />
** Create generated output file: ./EdGen -i input_t_test2.dat <br />
** Analyze the output (with TProof) of the generated file (files analysis_t_test2.C analysis_t_test2.h run_analysis_t_test2.C analysis_t_test2_output.root): root run_analysis_t_test2.C <br />
* Electron Production of  f'2->K_s0 +K_s0 (then each K_s0-> pi+ + pi-)  with t,q2,nu as input <br />
** Create generated output file: ./EdGen -i input_t_test3.dat <br />
** Analyze the output (with TProof) of the generated file (files analysis_t_test3.C analysis_t_test3.h run_analysis_t_test3.C analysis_t_test3_output.root): root run_analysis_t_test3.C <br />
* Electron Production of  f'2->K_s0 +K_s0 (then each K_s0-> pi+ + pi-)  with t,q2,nu as input <br />
** Create generated output file: ./EdGen -i input_t_test3.dat <br />
** Analyze the output (with TProof) of the generated file (files analysis_t_test3.C analysis_t_test3.h run_analysis_t_test3.C analysis_t_test3_output.root): root run_analysis_t_test3.C <br />