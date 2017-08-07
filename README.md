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
    
    
    
    
    