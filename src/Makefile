#  *****************************************************************
#  *                        PINOCCHIO  V4.1                        *
#  *  (PINpointing Orbit-Crossing Collapsed HIerarchical Objects)  *
#  *****************************************************************
#  
#  This code was written by
#  Pierluigi Monaco
#  Copyright (C) 2016
#  
#  web page: http://adlibitum.oats.inaf.it/monaco/pinocchio.html
#  
#  This program is free software; you can redistribute it and/or modify
#  it under the terms of the GNU General Public License as published by
#  the Free Software Foundation; either version 2 of the License, or
#  (at your option) any later version.
#  
#  This program is distributed in the hope that it will be useful,
#  but WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#  GNU General Public License for more details.
#  
#  You should have received a copy of the GNU General Public License
#  along with this program; if not, write to the Free Software
#  Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA  02111-1307  USA
#


RUNDIR = ./
EXAMPLEDIR = ../example/

EXEC = pinocchio
RUNPLAN = $(EXEC)_rp

PARAMS_FILE_PATH = $(EXAMPLEDIR)parameter_file
OUT_FILE_PATH = $(EXAMPLEDIR)outputs

PARAMS_OUT = $(EXEC)_params.conf
OUTS_OUT = $(EXEC)_outs.conf

DEBUG = NO
# TODO check OMP integration
OMP   = NO

#OPTIONS += -DCLASSIC_FRAGMENTATION
#OPTIONS += -DLIGHT_OUTPUT
#OPTIONS += -DSNAPSHOT

OPTIONS += -DTWO_LPT
OPTIONS += -DTHREE_LPT
OPTIONS += -DPLC
OPTIONS += -DNORADIATION
#OPTIONS += -DTABULATED_CT
#OPTIONS += -DELL_SNG
OPTIONS += -DELL_CLASSIC
#OPTIONS += -DSCALE_DEPENDENT
#OPTIONS += -DMOD_GRAV_FR
#OPTIONS += -DFR0=1.e-4
#OPTIONS += -DRECOMPUTE_DISPLACEMENTS

# output
#OPTIONS += -DADD_RMAX_TO_SNAPSHOT
#OPTIONS += -DLONGIDS
#OPTIONS += -DLIGHT_OUTPUT

# other options
#OPTIONS += -DWHITENOISE
#OPTIONS += -DNO_RANDOM_MODULES
#OPTIONS += -DDOUBLE_PRECISION_PRODUCTS

ifeq ($(OMP), YES)
#OPTIONS += -D_OPENMP
OPTIONS += -DUSE_FFT_THREADS
OMP_FLAG = -fopenmp
OMP_FFTW = -lfftw3_omp
else
OMP_FLAG =
OMP_FFTW =
endif

SYSTYPE ="Karabo"
LOCAL = NO

ifeq ($(LOCAL), YES)
DEPENDENCY_ROOT=$(CONDA_PREFIX)
else
DEPENDENCY_ROOT=$(PREFIX)
endif

ifeq ($(SYSTYPE),"Karabo")
CC          =  mpicc
CDEBUG      = -ggdb3 -Wall $(OMP_FLAG)
COPTIMIZED  = -O3 -Wno-unused-result $(OMP_FLAG)
FFTW_LIBR   = -L$(DEPENDENCY_ROOT)/lib -lfftw3_mpi -lfftw3 $(OMP_FFTW)
FFTW_INCL   = -I$(DEPENDENCY_ROOT)/include
PFFT_LIBR   = -L$(DEPENDENCY_ROOT)/lib -lpfft
PFFT_INCL   = -I$(DEPENDENCY_ROOT)/include
#MPI_LIBR    = -L$(SMPI_ROOT)/lib -lopenmpi
MPI_INCL    = -I$(SMPI_ROOT)/include
GSL_LIBR    = -L$(GSL_LIB)/lib -lgsl -lgslcblas -lm
GSL_INCL    = -I$(GSL_INC)
endif

INC =  $(PFFT_INCL) $(FFTW_INCL) $(MPI_INCL) $(GSL_INCL)
LIB =  -lm $(PFFT_LIBR) $(FFTW_LIBR) $(MPI_LIBR) $(GSL_LIBR)


# compiler options : choose from CDEBUG or COPTIMIZED
# compiler options
ifeq ($(DEBUG), YES)
COPTS = $(CDEBUG)
else
COPTS = $(COPTIMIZED)
endif

# source files:
OBJECTS = fmax.o variables.o initialization.o collapse_times.o fmax-pfft.o GenIC.o \
	ReadParamfile.o allocations.o LPT.o distribute.o \
	fragment.o build_groups.o write_halos.o write_snapshot.o cosmo.o
#ReadWhiteNoise.o

# rules
%.o: %.c Makefile pinocchio.h def_splines.h
	$(CC) $(INC) $(COPTS) $(OPTIONS) -c $<

all: pinocchio run_planner

pinocchio: $(OBJECTS) pinocchio.o Makefile
	$(CC) $(INC) $(COPTS) -o $(RUNDIR)$(EXEC) pinocchio.o $(OBJECTS) $(LIB)

run_planner: $(OBJECTS) run_planner.o Makefile
	$(CC) $(INC) $(COPTS) -o $(RUNDIR)$(RUNPLAN) run_planner.o $(OBJECTS) $(LIB)

cleanObjects:
	\rm -f *.o *~

cleanBinaries:
	\rm -f $(EXEC) $(RUNPLAN)

clean: cleanObjects cleanBinaries

install: 
	# install binaries
	install -d $(DEPENDENCY_ROOT)/bin/
	install -m 777 $(RUNDIR)$(EXEC) $(DEPENDENCY_ROOT)/bin/
	install -m 777 $(RUNDIR)$(RUNPLAN) $(DEPENDENCY_ROOT)/bin/
	
	# install example files
	install -d $(DEPENDENCY_ROOT)/etc/
	install -m 666 $(PARAMS_FILE_PATH) $(DEPENDENCY_ROOT)/etc/$(PARAMS_OUT)
	install -m 666 $(OUT_FILE_PATH) $(DEPENDENCY_ROOT)/etc/$(OUTS_OUT)
	