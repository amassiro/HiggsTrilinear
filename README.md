# HiggsTrilinear

Calculate reweighting function for VH:

Instructions:

    https://cp3.irmp.ucl.ac.be/projects/madgraph/wiki/HiggsSelfCoupling
    

    
    
    
    
    
1. Copy hhh-model in 'MG5_aMC_v2_5_5/models/'. 
   launch './bin/mg5_aMC' in 'MG5_aMC_v2_5_5' and generate 
   ZH process with following syntax, 
----------------------------------
>import model hhh-model
>generate p p > h z [LOonly= QCD]
>output hz_MC
>quit


and for WH (my instructions)

    >generate p p > w z [LOonly= QCD]
    >output hw_MC
    

    
EWK corrections:
    
    import model hhh-model
    generate d~ d > h z [virt=QED]
    add process u~ u > h z [virt=QED]
    add process u u~ > h z [virt=QED]
    add process d d~ > h z [virt=QED]
    
    output hz_ME
    quit
    


    cp ../trilinear-RW/makefile  hz_ME/SubProcesses/
    cp ../trilinear-RW/check_OLP.f  hz_ME/SubProcesses/
    cp check_olp.inc  hz_ME/SubProcesses/
    cp ../trilinear-RW/pmass.inc  hz_ME/SubProcesses/
    cp hz_ME/SubProcesses/P0_dxd_hz/pmass.inc hz_ME/SubProcesses/
    cp hz_ME/SubProcesses/P0_dxd_hz/nsqso_born.inc hz_ME/SubProcesses/
    cp hz_ME/SubProcesses/P0_dxd_hz/nsquaredSO.inc hz_ME/SubProcesses/
    cp hz_MC/SubProcesses/c_weight.inc hz_ME/SubProcesses/
    cp hz_MC/SubProcesses/P0_ddx_hz/nexternal.inc hz_ME/SubProcesses/

    
    
    
W+, step by step:
    
    
    cp -r ../trilinear-RW/hhh-model/     MG5_aMC_v2_5_5/models/
    
    ./bin/mg5_aMC
    
    
 
         import model hhh-model
         generate p p > w+ h [LOonly= QCD]
         output hwp_MC
         quit

    
    
    cp ../trilinear-RW/gevirt.sh .
    ./gevirt.sh hwp_MC
         
    cp ../trilinear-RW/vvh-loop_diagram_generation.py madgraph/loop/loop_diagram_generation.py

    
    ./bin/mg5_aMC    
    
         import model hhh-model
         generate u d~ > w+ h [virt=QED]
         add process d~ u > w+ h [virt=QED]
            
         output hwp_ME
         quit
 
 
    cp ../trilinear-RW/makefile  hwp_ME/SubProcesses/
    cp ../trilinear-RW/check_OLP.f  hwp_ME/SubProcesses/
    
    
    FIX: check_olp.inc 
          
          *        pdlabel='lhapdf'
               pdlabel='nn23nlo'

          *         lhaid=90500
                  lhaid=244600
    
    cp check_olp.inc  hwp_ME/SubProcesses/
    cp hwp_ME/SubProcesses/P0_udx_wph/pmass.inc hwp_ME/SubProcesses/
    cp hwp_ME/SubProcesses/P0_udx_wph/nsqso_born.inc hwp_ME/SubProcesses/
    cp hwp_ME/SubProcesses/P0_udx_wph/nsquaredSO.inc hwp_ME/SubProcesses/
    cp hwp_MC/SubProcesses/c_weight.inc hwp_ME/SubProcesses/
    cp hwp_MC/SubProcesses/P0_dxu_wph/nexternal.inc hwp_ME/SubProcesses/

    
    
    cp MYW/lib/libpdf.a hwp_ME/lib/
    cp ../../../install-LHAPDF/lib/libLHAPDF.a hwp_ME/lib/
    cp -r MYW/lib/Pdfdata hwp_ME/lib/Pdfdata/
    
    cp -r MYW/lib/PDFsets hwp_ME/lib/PDFsets/    ----> not found!!
    
    
    
    cd hwp_ME/SubProcesses
    
    make OLP_static
    make check_OLP



    set 'True = store rwgt info' in "hwp_MC/Cards/run_card.dat"

    cd ../../hwp_MC
    ./bin/generate_events
    1
    3
    
generate LO events in 'hwp_MC' with following options 
-----------------
fixedorder = OFF
shower     = OFF
reweight   = OFF
order      = LO
madspin    = OFF
-----------------


    cd ../hwp_ME/SubProcesses/
    cp ../../hwp_MC/Events/run_01_LO/events.lhe.gz  .
    gunzip events.lhe.gz
    

    ./check_OLP
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
W-
----


You are inside the 'trilinear-RW' folder.

1. Copy hhh-model in 'MG5_aMC_v2_5_5/models/'. 
   launch './bin/mg5_aMC' in 'MG5_aMC_v2_5_5' and generate 
   WH process with following syntax, 

./bin/mg5_aMC

----------------------------------
import model hhh-model
generate p p > h w- [LOonly= QCD]
output hwm_MC
quit
----------------------------------

2. copy "gevirt.sh" from 'trilinear-RW' in 'MG5_aMC_v2_5_5' and run './gevirt.sh hz_MC'
   (Gives two outputs: "check_olp.inc" & "proc_ml")

./gevirt.sh hwm_MC


3. copy "vvh-loop_diagram_generation.py"  from 'trilinear-RW' in 'madgraph/loop/' and 
   rename it as "loop_diagram_generation.py" 



4. generate EW virtual subprocesses collected in "proc_ml" using 'hhh-model' with output 'hz_ME'
   (DO NOT INSTALL 'collier'. In case you end up installing it, disable it in 
    "MG5_aMC_v2_5_5/input/mg5_configuration.txt" by setting 'collier = None' and remove the # in 
    front of it.)



./bin/mg5_aMC

----------------------------------
import model hhh-model
generate u~ d > h w- [virt=QED]
add process d u~ > h w- [virt=QED]
output hwm_ME
quit
----------------------------------




5. copy following files in 'hz_ME/SubProcesses/'
 "makefile", "check_OLP.f", "check_olp.inc" (provided+generated),
 "pmass.inc", "nsqso_born.inc", "nsquaredSO.inc" (from one of the subprocess folders in 'hz_ME'),
 "c_weight.inc" (from 'hz_MC/SubProcesses') and "nexternal.inc" (from one of the subprocess folders in 'hz_MC')

 
cp ../trilinear-RW/makefile  hwm_ME/SubProcesses/
cp ../trilinear-RW/check_OLP.f  hwm_ME/SubProcesses/
cp check_olp.inc  hwm_ME/SubProcesses/


cp hwm_ME/SubProcesses/P0_uxd_hwm/pmass.inc        hwm_ME/SubProcesses/
cp hwm_ME/SubProcesses/P0_uxd_hwm/nsqso_born.inc   hwm_ME/SubProcesses/
cp hwm_ME/SubProcesses/P0_uxd_hwm/nsquaredSO.inc   hwm_ME/SubProcesses/
cp hwm_MC/SubProcesses/c_weight.inc hwm_ME/SubProcesses/
cp hwm_MC/SubProcesses/P0_dux_hwm/nexternal.inc hwm_ME/SubProcesses/


 
6. copy "libpdf.a", "libLHAPDF.a", 'Pdfdata', 'PDFsets' from the 'lib' folder of any process already generated 
   in Madgraph to 'hz_ME/lib/'


cp MYW/lib/libpdf.a       hwm_ME/lib/
cp ../../../install-LHAPDF/lib/libLHAPDF.a hwm_ME/lib/
cp -r MYW/lib/Pdfdata hwm_ME/lib/Pdfdata/

cp -r MYW/lib/PDFsets hwm_ME/lib/PDFsets/    ----> not found!!



7. Go to 'hz_ME/SubProcesses/' folder and,  
----------------
make OLP_static
make check_OLP
----------------
   (the output is an executable file 'check_OLP')

cd hwm_ME/SubProcesses

make OLP_static
make check_OLP


8. set 'True = store rwgt info' in "hz_MC/Cards/run_card.dat"




9. generate LO events in 'hz_MC' with following options 
-----------------
fixedorder = OFF
shower     = OFF
reweight   = OFF
order      = LO
madspin    = OFF
-----------------

cd hwm_MC
./bin/generate_events
1
3



9. move the LO lhe event file (don't forget to unzip it!) to 'hz_ME/SubProcesses/' and execute './check_OLP'
   (note that the input file name should be "events.lhe"(unweighted) and you get an output file named 
   "events_rwgt.lhe" (weighted).)

cd ../hwm_ME/SubProcesses/
cp ../../hwm_MC/Events/run_01_LO/events.lhe.gz  .
gunzip events.lhe.gz
    
./check_OLP
    
    
    
10. The steps can be repeated for WH, VBF, tHj and ttH processes.

    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    

    
    
W+
----


You are inside the 'trilinear-RW' folder.

1. Copy hhh-model in 'MG5_aMC_v2_5_5/models/'. 
   launch './bin/mg5_aMC' in 'MG5_aMC_v2_5_5' and generate 
   WH process with following syntax, 

./bin/mg5_aMC

----------------------------------
import model hhh-model
generate p p > h w+ [LOonly= QCD]
output hwp_MC
quit
----------------------------------

2. copy "gevirt.sh" from 'trilinear-RW' in 'MG5_aMC_v2_5_5' and run './gevirt.sh hz_MC'
   (Gives two outputs: "check_olp.inc" & "proc_ml")

./gevirt.sh hwp_MC


3. copy "vvh-loop_diagram_generation.py"  from 'trilinear-RW' in 'madgraph/loop/' and 
   rename it as "loop_diagram_generation.py" 



4. generate EW virtual subprocesses collected in "proc_ml" using 'hhh-model' with output 'hz_ME'
   (DO NOT INSTALL 'collier'. In case you end up installing it, disable it in 
    "MG5_aMC_v2_5_5/input/mg5_configuration.txt" by setting 'collier = None' and remove the # in 
    front of it.)



./bin/mg5_aMC

----------------------------------
import model hhh-model
generate u d~ > h w+ [virt=QED]
add process d~ u > h w+ [virt=QED]
output hwp_ME
quit
----------------------------------




5. copy following files in 'hz_ME/SubProcesses/'
 "makefile", "check_OLP.f", "check_olp.inc" (provided+generated),
 "pmass.inc", "nsqso_born.inc", "nsquaredSO.inc" (from one of the subprocess folders in 'hz_ME'),
 "c_weight.inc" (from 'hz_MC/SubProcesses') and "nexternal.inc" (from one of the subprocess folders in 'hz_MC')

 
cp ../trilinear-RW/makefile  hwp_ME/SubProcesses/
cp ../trilinear-RW/check_OLP.f  hwp_ME/SubProcesses/
cp check_olp.inc  hwp_ME/SubProcesses/


cp hwp_ME/SubProcesses/P0_udx_hwp/pmass.inc        hwp_ME/SubProcesses/
cp hwp_ME/SubProcesses/P0_udx_hwp/nsqso_born.inc   hwp_ME/SubProcesses/
cp hwp_ME/SubProcesses/P0_udx_hwp/nsquaredSO.inc   hwp_ME/SubProcesses/
cp hwp_MC/SubProcesses/c_weight.inc hwp_ME/SubProcesses/
cp hwp_MC/SubProcesses/P0_dxu_hwp/nexternal.inc hwp_ME/SubProcesses/


 
6. copy "libpdf.a", "libLHAPDF.a", 'Pdfdata', 'PDFsets' from the 'lib' folder of any process already generated 
   in Madgraph to 'hz_ME/lib/'


cp MYW/lib/libpdf.a       hwp_ME/lib/
cp ../../../install-LHAPDF/lib/libLHAPDF.a hwp_ME/lib/
cp -r MYW/lib/Pdfdata hwp_ME/lib/Pdfdata/

cp -r MYW/lib/PDFsets hwp_ME/lib/PDFsets/    ----> not found!!



7. Go to 'hz_ME/SubProcesses/' folder and,  
----------------
make OLP_static
make check_OLP
----------------
   (the output is an executable file 'check_OLP')

cd hwp_ME/SubProcesses

make OLP_static
make check_OLP


8. set 'True = store rwgt info' in "hz_MC/Cards/run_card.dat"




9. generate LO events in 'hz_MC' with following options 
-----------------
fixedorder = OFF
shower     = OFF
reweight   = OFF
order      = LO
madspin    = OFF
-----------------

cd hwp_MC
./bin/generate_events
1
3



9. move the LO lhe event file (don't forget to unzip it!) to 'hz_ME/SubProcesses/' and execute './check_OLP'
   (note that the input file name should be "events.lhe"(unweighted) and you get an output file named 
   "events_rwgt.lhe" (weighted).)

cd ../hwp_ME/SubProcesses/
cp ../../hwp_MC/Events/run_01_LO/events.lhe.gz  .
gunzip events.lhe.gz
    
./check_OLP
    
    
    
10. The steps can be repeated for WH, VBF, tHj and ttH processes.

    
    
        
    
    
    
    
    
    
    
    
    
 

    
    
Z
----


You are inside the 'trilinear-RW' folder.

1. Copy hhh-model in 'MG5_aMC_v2_5_5/models/'. 
   launch './bin/mg5_aMC' in 'MG5_aMC_v2_5_5' and generate 
   WH process with following syntax, 

./bin/mg5_aMC

----------------------------------
import model hhh-model
generate p p > h z [LOonly= QCD]
output hz_MC
quit
----------------------------------

2. copy "gevirt.sh" from 'trilinear-RW' in 'MG5_aMC_v2_5_5' and run './gevirt.sh hz_MC'
   (Gives two outputs: "check_olp.inc" & "proc_ml")

./gevirt.sh hz_MC


3. copy "vvh-loop_diagram_generation.py"  from 'trilinear-RW' in 'madgraph/loop/' and 
   rename it as "loop_diagram_generation.py" 



4. generate EW virtual subprocesses collected in "proc_ml" using 'hhh-model' with output 'hz_ME'
   (DO NOT INSTALL 'collier'. In case you end up installing it, disable it in 
    "MG5_aMC_v2_5_5/input/mg5_configuration.txt" by setting 'collier = None' and remove the # in 
    front of it.)



./bin/mg5_aMC

----------------------------------
import model hhh-model
generate d~ d > h z [virt=QED]
add process u~ u > h z [virt=QED]
add process u u~ > h z [virt=QED]
add process d d~ > h z [virt=QED]
output hz_ME
quit
----------------------------------




5. copy following files in 'hz_ME/SubProcesses/'
 "makefile", "check_OLP.f", "check_olp.inc" (provided+generated),
 "pmass.inc", "nsqso_born.inc", "nsquaredSO.inc" (from one of the subprocess folders in 'hz_ME'),
 "c_weight.inc" (from 'hz_MC/SubProcesses') and "nexternal.inc" (from one of the subprocess folders in 'hz_MC')

 
cp ../trilinear-RW/makefile  hz_ME/SubProcesses/
cp ../trilinear-RW/check_OLP.f  hz_ME/SubProcesses/
cp check_olp.inc  hz_ME/SubProcesses/


cp hz_ME/SubProcesses/P0_dxd_hz/pmass.inc        hz_ME/SubProcesses/
cp hz_ME/SubProcesses/P0_dxd_hz/nsqso_born.inc   hz_ME/SubProcesses/
cp hz_ME/SubProcesses/P0_dxd_hz/nsquaredSO.inc   hz_ME/SubProcesses/
cp hz_MC/SubProcesses/c_weight.inc hz_ME/SubProcesses/
cp hz_MC/SubProcesses/P0_ddx_hz/nexternal.inc hz_ME/SubProcesses/


 
6. copy "libpdf.a", "libLHAPDF.a", 'Pdfdata', 'PDFsets' from the 'lib' folder of any process already generated 
   in Madgraph to 'hz_ME/lib/'


cp MYW/lib/libpdf.a       hz_ME/lib/
cp ../../../install-LHAPDF/lib/libLHAPDF.a hz_ME/lib/
cp -r MYW/lib/Pdfdata hz_ME/lib/Pdfdata/

cp -r MYW/lib/PDFsets hz_ME/lib/PDFsets/    ----> not found!!



7. Go to 'hz_ME/SubProcesses/' folder and,  
----------------
make OLP_static
make check_OLP
----------------
   (the output is an executable file 'check_OLP')

cd hz_ME/SubProcesses

make OLP_static
make check_OLP


8. set 'True = store rwgt info' in "hz_MC/Cards/run_card.dat"




9. generate LO events in 'hz_MC' with following options 
-----------------
fixedorder = OFF
shower     = OFF
reweight   = OFF
order      = LO
madspin    = OFF
-----------------

cd hz_MC
./bin/generate_events
1
3



9. move the LO lhe event file (don't forget to unzip it!) to 'hz_ME/SubProcesses/' and execute './check_OLP'
   (note that the input file name should be "events.lhe"(unweighted) and you get an output file named 
   "events_rwgt.lhe" (weighted).)

cd ../hz_ME/SubProcesses/
cp ../../hz_MC/Events/run_01_LO/events.lhe.gz  .
gunzip events.lhe.gz
    
./check_OLP
    
    
    
10. The steps can be repeated for WH, VBF, tHj and ttH processes.

    
        
        
        
        
        
        
LHE analyzer
====


./ntupleMaker.exe  ../HiggsTrilinear/MG5_aMC_v2_5_5/hwm_ME/SubProcesses/events_rwgt.lhe wm_after.root
./ntupleMaker.exe  ../HiggsTrilinear/MG5_aMC_v2_5_5/hwm_ME/SubProcesses/events.lhe      wm_before.root

./ntupleMaker.exe  ../HiggsTrilinear/MG5_aMC_v2_5_5/hwp_ME/SubProcesses/events_rwgt.lhe wp_after.root
./ntupleMaker.exe  ../HiggsTrilinear/MG5_aMC_v2_5_5/hwp_ME/SubProcesses/events.lhe      wp_before.root
 
./ntupleMaker.exe  ../HiggsTrilinear/MG5_aMC_v2_5_5/hz_ME/SubProcesses/events_rwgt.lhe  z_after.root
./ntupleMaker.exe  ../HiggsTrilinear/MG5_aMC_v2_5_5/hz_ME/SubProcesses/events.lhe       z_before.root
 
 
cd test

r00t -l draw.cxx\(\"../wm_before.root\",\"../wm_after.root\",\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)
r00t -l draw.cxx\(\"../wp_before.root\",\"../wp_after.root\",\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)
r00t -l draw.cxx\(\"../z_before.root\",\"../z_after.root\",\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)




r00t -l drawKl.cxx\(\"../wm_before.root\",\"../wm_after.root\",1,\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)
r00t -l drawKl.cxx\(\"../wp_before.root\",\"../wp_after.root\",1,\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)
r00t   -l drawKl.cxx\(\"../z_before.root\",\"../z_after.root\",1,\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)


r00t -l drawKl.cxx\(\"../wm_before.root\",\"../wm_after.root\",10,\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)
r00t -l drawKl.cxx\(\"../wp_before.root\",\"../wp_after.root\",10,\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)
r00t   -l drawKl.cxx\(\"../z_before.root\",\"../z_after.root\",10,\"pth1[0]\",20,0,200,\"p_{T}^{H}\"\)





 
 