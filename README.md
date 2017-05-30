# WRFextractAODbyLayerr
READ AOD by COLUMN FILES BY STATIONS
; SAVE EXTRACTED VARIABLES and time INTO A MATRIX using loop

  load "$NCARG_ROOT/lib/ncarg/nclscripts/csm/gsn_code.ncl"
  load "$NCARG_ROOT/lib/ncarg/nclscripts/wrf/WRFUserARW.ncl"

  start_date = "2016-05-03_00:00:00"
  wrf_folder = "/Shared/CGRER-Scratch/mgao2/KORUS-AQ/forecasts/"

; ::::::::::::OPEN WRF - READ VARS:::::::::::::::::::::::::::::::::::::::::::::::

  FILES = systemfunc(" ls "+wrf_folder+"wrfout_d02*")
  ;FILES = systemfunc(" ls "+wrf_folder+"wrfout_d02*2016-05-03_2*")
  ;num_files = dimsizes(FILES)
  num_files = 10
  num_col = 52
  ;print(num_files)
  data_plot = new ((/num_files,num_col/),float)     ;52 columns for each AOD layer
  WRF_Time = new(num_files,string)

  do hr=0,num_files-1         ; size of the loop that goes from 0 to 1091
  print("Reading file:" +"file "+hr+ " : "+ FILES(hr))

     time = 0
     wrf_file = addfile(FILES(hr),"r")

     lat   = (/  37.55  /)     ; Seoul
     lon   = (/  126.95 /)      ; return 2d subscripts
     lat2d = wrf_file->XLAT(0,:,:)
     lon2d = wrf_file->XLONG(0,:,:)
     nm = getind_latlon2d (lat2d,lon2d, lat, lon)
     ;nm=indices of closest stations of specified latlon
     n = nm(0,0)
     m = nm(0,1)

     WRF_Time(hr) = wrf_user_getvar(wrf_file,"times",-1) ;Find the time stamp
     AOD400 = wrf_user_getvar(wrf_file,"TAUAER2",time)        ;aod 400
     AOD600 = wrf_user_getvar(wrf_file,"TAUAER3",time)        ;aod 600
     Angstrom_Exp = log(AOD600/AOD400)/log(600.0/400.0) ;Calculate the Angstrom exponent
     AOD_500 = AOD400*(500.0/400.0)^Angstrom_Exp ;Calculate the AOD in 550 nm wavelength

    do i=0,50
        data_plot(hr,i) = AOD_500(i,n,m)
    end do


  end do

 printVarSummary(data_plot)

; ::::::::::::::: Save Output ::::::::::::::::::::::::::::::::::::::::::::

;outfile = "SeoulAOD500byLayer.txt1"
outfile = "try1.txt"
READ AOD by COLUMN FILES BY STATIONS
; SAVE EXTRACTED VARIABLES and time INTO A MATRIX using loop

  load "$NCARG_ROOT/lib/ncarg/nclscripts/csm/gsn_code.ncl"
  load "$NCARG_ROOT/lib/ncarg/nclscripts/wrf/WRFUserARW.ncl"

  start_date = "2016-05-03_00:00:00"
  wrf_folder = "/Shared/CGRER-Scratch/mgao2/KORUS-AQ/forecasts/"

; ::::::::::::OPEN WRF - READ VARS:::::::::::::::::::::::::::::::::::::::::::::::

  FILES = systemfunc(" ls "+wrf_folder+"wrfout_d02*")
  ;FILES = systemfunc(" ls "+wrf_folder+"wrfout_d02*2016-05-03_2*")
  ;num_files = dimsizes(FILES)
  num_files = 10
  num_col = 52
  ;print(num_files)
  data_plot = new ((/num_files,num_col/),float)     ;52 columns for each AOD layer
  WRF_Time = new(num_files,string)

  do hr=0,num_files-1         ; size of the loop that goes from 0 to 1091
  print("Reading file:" +"file "+hr+ " : "+ FILES(hr))

     time = 0
     wrf_file = addfile(FILES(hr),"r")

     lat   = (/  37.55  /)     ; Seoul
     lon   = (/  126.95 /)      ; return 2d subscripts
     lat2d = wrf_file->XLAT(0,:,:)
     lon2d = wrf_file->XLONG(0,:,:)
     nm = getind_latlon2d (lat2d,lon2d, lat, lon)
     ;nm=indices of closest stations of specified latlon
     n = nm(0,0)
     m = nm(0,1)

     WRF_Time(hr) = wrf_user_getvar(wrf_file,"times",-1) ;Find the time stamp
     AOD400 = wrf_user_getvar(wrf_file,"TAUAER2",time)        ;aod 400
     AOD600 = wrf_user_getvar(wrf_file,"TAUAER3",time)        ;aod 600
     Angstrom_Exp = log(AOD600/AOD400)/log(600.0/400.0) ;Calculate the Angstrom exponent
     AOD_500 = AOD400*(500.0/400.0)^Angstrom_Exp ;Calculate the AOD in 550 nm wavelength

    do i=0,50
        data_plot(hr,i) = AOD_500(i,n,m)
    end do


  end do

 printVarSummary(data_plot)

; ::::::::::::::: Save Output ::::::::::::::::::::::::::::::::::::::::::::

;outfile = "SeoulAOD500byLayer.txt1"
outfile = "try1.txt"
dimx = dimsizes(data_plot)
nrows = dimx(0)
lines = new(nrows, string)
do i = 0,nrows-1
 lines(i) = str_concat(sprintf("%7.5f ", data_plot(i,:))) ;Concanetate different cols into lines, for output
end do
        write_table(outfile, "w", [/WRF_Time,lines/],"%s %s")


