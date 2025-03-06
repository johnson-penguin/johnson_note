---
title: TM500+OAI Configuration File

---

- [UNH example.xml](https://drive.google.com/file/d/1odCVLPw2p4e0Vg1JQ82rEMtZxzmcNzyA/view?usp=drive_link)
- [viavi-tm500-oai-gnb.conf](https://drive.google.com/file/d/1xKsDkcdN2E5JI7aBmJ1280E1eZ6MC-rl/view?usp=drive_link)
- [5_6088871686956912720.sh](https://drive.google.com/file/d/1uQYQGlKg-1rqisaKKF3p5vr7IKnbcwWs/view?usp=drive_link)


### OAI BBU Configuration 
- Path:`FH_7.2_dev/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.4x4-liteon.conf`
#### BMWLAB (BBU version)
:::spoiler OAI Configuration file original one
```c=
Active_gNBs = ( "gNB-OAI");
# Asn1_verbosity, choice in: none, info, annoying
Asn1_verbosity = "none";

gNBs =
(
 {
    ////////// Identification parameters:
    gNB_ID    =  0xe00;
    gNB_name  =  "gNB-OAI";

    // Tracking area code, 0x0000 and 0xfffe are reserved values
    tracking_area_code  =  1;
    plmn_list = ({ mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1; }); });

    nr_cellid = 1;

    ////////// Physical parameters:

    pdsch_AntennaPorts_XP = 2; 
    pusch_AntennaPorts    = 4; 
    do_CSIRS              = 1;
    do_SRS                = 0 ;
    force_256qam_off      = 1;
    sib1_tda			  = 15;

    pdcch_ConfigSIB1 = (
      {
        controlResourceSetZero = 11;
        searchSpaceZero = 0;
      }
    );

    servingCellConfigCommon = (
    {
 #spCellConfigCommon

      physCellId                                                    = 0;
     # n_TimingAdvanceOffset                                         = 0;
#  downlinkConfigCommon
    #frequencyInfoDL
      # this is 3450.72 MHz (center frequency)
      absoluteFrequencySSB                                          = 630048;
      dl_frequencyBand                                              = 78;
      # this is 3401.58 MHz
      dl_absoluteFrequencyPointA                                    = 626772;
      #scs-SpecificCarrierList
        dl_offstToCarrier                                           = 0;
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
        dl_subcarrierSpacing                                        = 1;
        dl_carrierBandwidth                                         = 273;
     #initialDownlinkBWP
      #genericParameters
       initialDLBWPlocationAndBandwidth                             = 1099; #38.101-1 Table 5.3.2-1
       #
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
        initialDLBWPsubcarrierSpacing                               = 1;
      #pdcch-ConfigCommon
        initialDLBWPcontrolResourceSetZero                          = 11;
        initialDLBWPsearchSpaceZero                                 = 0;

  #uplinkConfigCommon
     #frequencyInfoUL
      ul_frequencyBand                                              = 78;
      #scs-SpecificCarrierList
      ul_offstToCarrier                                             = 0;
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
      ul_subcarrierSpacing                                          = 1;
      ul_carrierBandwidth                                           = 273;
      pMax                                                          = 23;
     #initialUplinkBWP
      #genericParameters
        initialULBWPlocationAndBandwidth                            = 1099;
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
        initialULBWPsubcarrierSpacing                               = 1;
      #rach-ConfigCommon
        #rach-ConfigGeneric
          prach_ConfigurationIndex                                  = 159;
#prach_msg1_FDM
#0 = one, 1=two, 2=four, 3=eight
          prach_msg1_FDM                                            = 0;
          prach_msg1_FrequencyStart                                 = 0;
          zeroCorrelationZoneConfig                                 = 15;
          preambleReceivedTargetPower                               = -104;
#preamblTransMax (0...10) = (3,4,5,6,7,8,10,20,50,100,200)
          preambleTransMax                                          = 7;
#powerRampingStep
# 0=dB0,1=dB2,2=dB4,3=dB6
        powerRampingStep                                            = 2;
#ra_ReponseWindow
#1,2,4,8,10,20,40,80
        ra_ResponseWindow                                           = 5;
#ssb_perRACH_OccasionAndCB_PreamblesPerSSB_PR
#1=oneeighth,2=onefourth,3=half,4=one,5=two,6=four,7=eight,8=sixteen
        ssb_perRACH_OccasionAndCB_PreamblesPerSSB_PR                = 3;
#oneHalf (0..15) 4,8,12,16,...60,64
        ssb_perRACH_OccasionAndCB_PreamblesPerSSB                   = 15;
#ra_ContentionResolutionTimer
#(0..7) 8,16,24,32,40,48,56,64
        ra_ContentionResolutionTimer                                = 7;
        rsrp_ThresholdSSB                                           = 19;
#prach-RootSequenceIndex_PR
#1 = 839, 2 = 139
        prach_RootSequenceIndex_PR                                  = 2;
        prach_RootSequenceIndex                                     = 1;
        # SCS for msg1, can only be 15 for 30 kHz < 6 GHz, takes precendence over the one derived from prach-ConfigIndex
        #
        msg1_SubcarrierSpacing                                      = 1,
# restrictedSetConfig
# 0=unrestricted, 1=restricted type A, 2=restricted type B
        restrictedSetConfig                                         = 0,

# this is the offset between the last PRACH preamble power and the Msg3 PUSCH, 2 times the field value in dB
        msg3_DeltaPreamble                                          = 2;
        p0_NominalWithGrant                                         = -96;

# pucch-ConfigCommon setup :
# pucchGroupHopping
# 0 = neither, 1= group hopping, 2=sequence hopping
        pucchGroupHopping                                           = 0;
        hoppingId                                                   = 0;
        p0_nominal                                                  = -96;
# ssb_PositionsInBurs_BitmapPR
# 1=short, 2=medium, 3=long
      ssb_PositionsInBurst_PR                                       = 2;
      ssb_PositionsInBurst_Bitmap                                   = 0x1;

# ssb_periodicityServingCell
# 0 = ms5, 1=ms10, 2=ms20, 3=ms40, 4=ms80, 5=ms160, 6=spare2, 7=spare1
      ssb_periodicityServingCell                                    = 2;

# dmrs_TypeA_position
# 0 = pos2, 1 = pos3
      dmrs_TypeA_Position                                           = 0;

# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
      subcarrierSpacing                                             = 1;


  #tdd-UL-DL-ConfigurationCommon
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
      referenceSubcarrierSpacing                                    = 1;
      # pattern1
      # dl_UL_TransmissionPeriodicity
      # 0=ms0p5, 1=ms0p625, 2=ms1, 3=ms1p25, 4=ms2, 5=ms2p5, 6=ms5, 7=ms10
      dl_UL_TransmissionPeriodicity                                 = 5;
      nrofDownlinkSlots                                             = 3;
      nrofDownlinkSymbols                                           = 6;
      nrofUplinkSlots                                               = 1;
      nrofUplinkSymbols                                             = 4;

  ssPBCH_BlockPower                                                 = 0;
  }

  );


    # ------- SCTP definitions
    SCTP :
    {
        # Number of streams to use in input/output
        SCTP_INSTREAMS  = 2;
        SCTP_OUTSTREAMS = 2;
    };


    ////////// AMF parameters:
    amf_ip_address = ({ ipv4 = "192.168.8.21"; });

    NETWORK_INTERFACES :
    {
        GNB_IPV4_ADDRESS_FOR_NG_AMF              = "192.168.8.29/24";
        GNB_IPV4_ADDRESS_FOR_NGU                 = "192.168.8.29/24";
        GNB_PORT_FOR_S1U                         = 2152; # Spec 2152
    };
  }
);

MACRLCs = (
{
  num_cc                      = 1;
  tr_s_preference             = "local_L1";
  tr_n_preference             = "local_RRC";
  pusch_TargetSNRx10          = 300;
  pucch_TargetSNRx10          = 200;
 # dl_bler_target_upper=.35;
 # dl_bler_target_lower=.15; 
  ul_bler_target_upper=.35;
  ul_bler_target_lower=.15;
  pusch_FailureThres          = 100;
}
);

L1s = (
{
  num_cc = 1;
  tr_n_preference = "local_mac";
  prach_dtx_threshold = 130
  pucch0_dtx_threshold = 80;
  pusch_dtx_threshold = 10;
  tx_amp_backoff_dB = 20; # needs to match O-RU configuration
  L1_rx_thread_core = 8;
  L1_tx_thread_core = 10;
  phase_compensation = 1; # needs to match O-RU configuration
}
);

RUs = (
{
  local_rf       = "no";
  nb_tx          = 4;
  nb_rx          = 4;
  att_tx         = 0; # TODO relevant?
  att_rx         = 0; # TODO relevant?
  bands          = [78];
  max_pdschReferenceSignalPower = -27;
  max_rxgain                    = 75; # TODO relevant?
  sf_extension                  = 0; # TODO relevant?
  eNB_instances  = [0]; # TODO is relevant?
  ru_thread_core = 6;
  sl_ahead       = 5;
  ##beamforming 1x2 matrix: 1 layer x 2 antennas
  bf_weights = [0x00007fff, 0x0000,0x00007fff, 0x0000]; # necessary?

  tr_preference  = "raw_if4p5"; # important: activate FHI7.2
  do_precoding = 0; # needs to match O-RU configuration
  
}
);

security = {
  # preferred ciphering algorithms
  # the first one of the list that an UE supports in chosen
  # valid values: nea0, nea1, nea2, nea3
  ciphering_algorithms = ( "nea0" );

  # preferred integrity algorithms
  # the first one of the list that an UE supports in chosen
  # valid values: nia0, nia1, nia2, nia3
  integrity_algorithms = ( "nia2", "nia0" );

  # setting 'drb_ciphering' to "no" disables ciphering for DRBs, no matter
  # what 'ciphering_algorithms' configures; same thing for 'drb_integrity'
  drb_ciphering = "yes";
  drb_integrity = "no";
};

log_config :
{
  global_log_level                      ="info";
  hw_log_level                          ="info";
  phy_log_level                         ="info";
  mac_log_level                         ="info";
  rlc_log_level                         ="info";
  pdcp_log_level                        ="info";
  rrc_log_level                         ="info";
  ngap_log_level                        ="info";
  f1ap_log_level                        ="info";
};

fhi_72 = {
  dpdk_devices = ("0000:3b:0a.0", "0000:3b:0a.1");
  system_core = 0;
  io_core = 4;
  worker_cores = (2);
  du_addr = ("00:11:22:33:44:66", "00:11:22:33:44:66");
  ru_addr = ("00:aa:ff:bb:ff:cc", "00:aa:ff:bb:ff:cc");
  mtu = 1500; # check if xran uses this properly
  fh_config = ({
    Tadv_cp_dl = 125;
    T2a_cp_dl = (74, 464); # (min, max)
    T2a_cp_ul = (36, 392); # (min, max)
    T2a_up = (80, 464); # (min, max)
    Ta3 = (84, 120); # (min, max)
    T1a_cp_dl = (285, 429); # (min, max)
    T1a_cp_ul = (285, 429); # (min, max)
    T1a_up = (96, 196); # (min, max)
    Ta4 = (110, 180); # (min, max)
    ru_config = {
      iq_width = 8;
      iq_width_prach = 8;
      fft_size = 12;
    };
    prach_config = {
      eAxC_offset = 4;
      kbar = 0;
    };
  });
};


```
:::
#### VIAVI (BBU version)
:::spoiler OAI Configuration file provide by VIAVAI
```c=
###################################################################
# OAI gNodeB configuration file

Active_gNBs = ( "gNB-OAI-2");
# Asn1_verbosity, choice in: none, info, annoying
Asn1_verbosity = "none";

gNBs =
(
 {
    ////////// Identification parameters:
    gNB_ID    =  0xe01;
    gNB_name  =  "gNB-OAI-2";

    // Tracking area code, 0x0000 and 0xfffe are reserved values
    tracking_area_code  =  100;
    plmn_list = ({ mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1; }); });

    nr_cellid = 1;

    ////////// Physical parameters:

    pdsch_AntennaPorts_XP = 2; 
    pusch_AntennaPorts    = 2; 
    do_CSIRS              = 1;
    do_SRS                = 0 ;
    sib1_tda			  = 15;

    pdcch_ConfigSIB1 = (
      {
        controlResourceSetZero = 11;
        searchSpaceZero = 0;
      }
    );

    servingCellConfigCommon = (
    {
 #spCellConfigCommon

      physCellId                                                    = 0;
     # n_TimingAdvanceOffset                                         = 0;
#  downlinkConfigCommon
    #frequencyInfoDL
      # this is 3450.72 MHz (center frequency)
      absoluteFrequencySSB                                          = 630048;
      dl_frequencyBand                                              = 78;
      # this is 3401.58 MHz
      dl_absoluteFrequencyPointA                                    = 626772;
      #scs-SpecificCarrierList
        dl_offstToCarrier                                           = 0;
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
        dl_subcarrierSpacing                                        = 1;
        dl_carrierBandwidth                                         = 273;
     #initialDownlinkBWP
      #genericParameters
       initialDLBWPlocationAndBandwidth                             = 1099; #38.101-1 Table 5.3.2-1
       #
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
        initialDLBWPsubcarrierSpacing                               = 1;
      #pdcch-ConfigCommon
        initialDLBWPcontrolResourceSetZero                          = 11;
        initialDLBWPsearchSpaceZero                                 = 0;

  #uplinkConfigCommon
     #frequencyInfoUL
      ul_frequencyBand                                              = 78;
      #scs-SpecificCarrierList
      ul_offstToCarrier                                             = 0;
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
      ul_subcarrierSpacing                                          = 1;
      ul_carrierBandwidth                                           = 273;
      pMax                                                          = 23;
     #initialUplinkBWP
      #genericParameters
        initialULBWPlocationAndBandwidth                            = 1099;
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
        initialULBWPsubcarrierSpacing                               = 1;
      #rach-ConfigCommon
        #rach-ConfigGeneric
          prach_ConfigurationIndex                                  = 151;
#prach_msg1_FDM
#0 = one, 1=two, 2=four, 3=eight
          prach_msg1_FDM                                            = 0;
          prach_msg1_FrequencyStart                                 = 0;
          zeroCorrelationZoneConfig                                 = 0;
          preambleReceivedTargetPower                               = -100;
#preamblTransMax (0...10) = (3,4,5,6,7,8,10,20,50,100,200)
          preambleTransMax                                          = 7;
#powerRampingStep
# 0=dB0,1=dB2,2=dB4,3=dB6
        powerRampingStep                                            = 3;
#ra_ReponseWindow
#1,2,4,8,10,20,40,80
        ra_ResponseWindow                                           = 5;
#ssb_perRACH_OccasionAndCB_PreamblesPerSSB_PR
#1=oneeighth,2=onefourth,3=half,4=one,5=two,6=four,7=eight,8=sixteen
        ssb_perRACH_OccasionAndCB_PreamblesPerSSB_PR                = 3;
#oneHalf (0..15) 4,8,12,16,...60,64
        ssb_perRACH_OccasionAndCB_PreamblesPerSSB                   = 15;
#ra_ContentionResolutionTimer
#(0..7) 8,16,24,32,40,48,56,64
        ra_ContentionResolutionTimer                                = 7;
        rsrp_ThresholdSSB                                           = 19;
#prach-RootSequenceIndex_PR
#1 = 839, 2 = 139
        prach_RootSequenceIndex_PR                                  = 2;
        prach_RootSequenceIndex                                     = 1;
        # SCS for msg1, can only be 15 for 30 kHz < 6 GHz, takes precendence over the one derived from prach-ConfigIndex
        #
        msg1_SubcarrierSpacing                                      = 1,
# restrictedSetConfig
# 0=unrestricted, 1=restricted type A, 2=restricted type B
        restrictedSetConfig                                         = 0,

# this is the offset between the last PRACH preamble power and the Msg3 PUSCH, 2 times the field value in dB
        msg3_DeltaPreamble                                          = 2;
        p0_NominalWithGrant                                         = -96;

# pucch-ConfigCommon setup :
# pucchGroupHopping
# 0 = neither, 1= group hopping, 2=sequence hopping
        pucchGroupHopping                                           = 0;
        hoppingId                                                   = 0;
        p0_nominal                                                  = -96;
# ssb_PositionsInBurs_BitmapPR
# 1=short, 2=medium, 3=long
      ssb_PositionsInBurst_PR                                       = 2;
      ssb_PositionsInBurst_Bitmap                                   = 0x1;

# ssb_periodicityServingCell
# 0 = ms5, 1=ms10, 2=ms20, 3=ms40, 4=ms80, 5=ms160, 6=spare2, 7=spare1
      ssb_periodicityServingCell                                    = 2;

# dmrs_TypeA_position
# 0 = pos2, 1 = pos3
      dmrs_TypeA_Position                                           = 0;

# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
      subcarrierSpacing                                             = 1;


  #tdd-UL-DL-ConfigurationCommon
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
      referenceSubcarrierSpacing                                    = 1;
      # pattern1
      # dl_UL_TransmissionPeriodicity
      # 0=ms0p5, 1=ms0p625, 2=ms1, 3=ms1p25, 4=ms2, 5=ms2p5, 6=ms5, 7=ms10
      dl_UL_TransmissionPeriodicity                                 = 6;
      nrofDownlinkSlots                                             = 7;
      nrofDownlinkSymbols                                           = 6;
      nrofUplinkSlots                                               = 2;
      nrofUplinkSymbols                                             = 4;

  ssPBCH_BlockPower                                                 = 10;
  }

  );


    # ------- SCTP definitions
    SCTP :
    {
        # Number of streams to use in input/output
        SCTP_INSTREAMS  = 30;
        SCTP_OUTSTREAMS = 30;
    };


    ////////// AMF parameters:
    amf_ip_address      = ( { ipv4       = "10.16.2.2";
                              ipv6       = "192:168:30::17";
                              active     = "yes";
                              preference = "ipv4";
                            }
                          );

    NETWORK_INTERFACES :
    {
        GNB_INTERFACE_NAME_FOR_NG_AMF            = "ens3f0";
        GNB_IPV4_ADDRESS_FOR_NG_AMF              = "10.16.1.24/24";
        GNB_INTERFACE_NAME_FOR_NGU               = "ens3f0";
        GNB_IPV4_ADDRESS_FOR_NGU                 = "10.16.1.24/24";
        GNB_PORT_FOR_S1U                         = 2152; # Spec 2152
    };
  }
);

MACRLCs = (
{
  num_cc                      = 1;
  tr_s_preference             = "local_L1";
  tr_n_preference             = "local_RRC";
  pusch_TargetSNRx10          = 300;
  pucch_TargetSNRx10          = 230;
  dl_bler_target_upper=.35;
  dl_bler_target_lower=.15; 
  ul_bler_target_upper=.35;
  ul_bler_target_lower=.15;
  pusch_FailureThres          = 100;
  ul_max_mcs                  = 28;
}
);

L1s = (
{
  num_cc = 1;
  tr_n_preference = "local_mac";
  prach_dtx_threshold = 100;
  pucch0_dtx_threshold = 80;
  pusch_dtx_threshold = 10;
  max_ldpc_iterations = 10;
  tx_amp_backoff_dB = 20; # needs to match O-RU configuration
  L1_rx_thread_core = 0;
  L1_tx_thread_core = 1;
  phase_compensation = 0; # needs to match O-RU configuration
}
);

RUs = (
{
  local_rf       = "no";
  nb_tx          = 4;
  nb_rx          = 4;
  att_tx         = 0
  att_rx         = 0;
  bands          = [78];
  max_pdschReferenceSignalPower = -27;
  max_rxgain                    = 75;
  sf_extension                  = 0;
  eNB_instances  = [0];
  ru_thread_core = 2;
  sl_ahead       = 10;
  ##beamforming 1x2 matrix: 1 layer x 2 antennas
  bf_weights = [0x00007fff, 0x0000,0x00007fff, 0x0000];
  tr_preference  = "raw_if4p5"; # important: activate FHI7.2
  do_precoding = 0; # needs to match O-RU configuration
}
);

security = {
  # preferred ciphering algorithms
  # the first one of the list that an UE supports in chosen
  # valid values: nea0, nea1, nea2, nea3
  ciphering_algorithms = ( "nea0" );

  # preferred integrity algorithms
  # the first one of the list that an UE supports in chosen
  # valid values: nia0, nia1, nia2, nia3
  integrity_algorithms = ( "nia2", "nia0" );

  # setting 'drb_ciphering' to "no" disables ciphering for DRBs, no matter
  # what 'ciphering_algorithms' configures; same thing for 'drb_integrity'
  drb_ciphering = "yes";
  drb_integrity = "no";
};

log_config :
{
  global_log_level                      ="info";
  hw_log_level                          ="info";
  phy_log_level                         ="info";
  mac_log_level                         ="info";
  rlc_log_level                         ="debug";
  pdcp_log_level                        ="info";
  rrc_log_level                         ="debug";
  ngap_log_level                        ="info";
  f1ap_log_level                        ="info";
};

fhi_72 = {
  dpdk_devices = ("0000:12:02.0", "0000:12:02.1");
  system_core = 3;
  io_core = 8;
  worker_cores = (9,10);
  du_addr = ("00:11:22:33:44:67", "00:11:22:33:44:67");
  ru_addr = ("58:A2:E1:61:A2:23", "58:A2:E1:61:A2:23");
  mtu = 9216;
  file_prefix = "fhi_72";
  fh_config = ({

    Tadv_cp_dl = 125;
    T2a_cp_dl = (259, 470);
    T2a_cp_ul = (125, 1200);
    T2a_up = (70, 345);
    Ta3 = (50, 171);
    T1a_cp_dl = (258, 392);
    T1a_cp_ul = (285, 300);
    T1a_up = (155, 300);
    Ta4 = (0, 200);

    ru_config = {
      iq_width = 9;
      iq_width_prach = 9;
      fft_size = 12;
    };
    prach_config = {
      eAxC_offset = 4;
    };
  });
};

```
:::
#### Compare
:::success


|                               | BMWLAB     | VIAVI       |
|:----------------------------- |:---------- |:----------- |
| tracking_area_code            | 1          | 100         |
| pusch_AntennaPorts            | 4          | 2           |
| prach_ConfigurationIndex      | 159        | 151         |
| zeroCorrelationZoneConfig     | 15         | 0           |
| preambleReceivedTargetPower   | -104       | -100        |
| powerRampingStep              | 2          | 3           |
| dl_UL_TransmissionPeriodicity | 5          | 6           |
| nrofDownlinkSlots             | 3          | 7           |
| nrofUplinkSlots               | 1          | 2           |
| ssPBCH_BlockPower             | 0          | 10          |
| SCTP definitions              |            |             |
| SCTP_INSTREAMS                | 2          | 30          |
| SCTP_OUTSTREAMS               | 2          | 30          |
| MACRLCs                       |            |             |
| pucch_TargetSNRx10            | 200        | 230         |
| dl_bler_target_upper          | NA         | .35         |
| dl_bler_target_lower          | NA         | .15         |
| ul_max_mcs                    | NA         | 28          |
| L1s                           |            |             |
| prach_dtx_threshold           | 130        | 100         |
| max_ldpc_iterations           | NA         | 10          |
| L1_rx_thread_core             | 8          | 0           |
| L1_tx_thread_core             | 10         | 1           |
| phase_compensation            | 1          | 0           |
| RUs                           |            |             |
| ru_thread_core                | 6          | 2           |
| sl_ahead                      | 5          | 10          |
| fhi_72                        |            |             |
| system_core                   | 0          | 3           |
| io_core                       | 4          | 8           |
| worker_cores                  | 2          | (9,10)      |
| mtu                           | 1500       | 9216        |
| Timing Windows                |            |             |
| T2a_cp_dl                     | (74, 464)  | (259, 470)  |
| T2a_cp_ul                     | (36, 392)  | (125, 1200) |
| T2a_up                        | (80, 464)  | (70, 345)   |
| Ta3                           | (84, 120)  | (50, 171)   |
| T1a_cp_dl                     | (285, 429) | (258, 392)  |
| T1a_cp_ul                     | (285, 429) | (285, 300)  |
| T1a_up                        | (96, 196)  | (155, 300)  |
| Ta4                           | (110, 180) | (0, 200)    |
| ru_config                     |            |             |
| iq_width                      | 8          | 9           |
| iq_width_prach                | 8          | 9           |
| kbar                          | 0          | NA          |


---







### TM500 
#### 5_6088871686956912720.sh
:::spoiler 5_6088871686956912720.sh
```c=
#!/usr/bin/bash

# Description
# This is a script which collect Linux System information.
# sometimes file location may vary as per environment, if script throws some error, 
# make sure to cross check the envirnment.

# Usage:
#   1) Set FlexRAN environment
#   2) chmod +x ./get_system_info.txt
#   3) /usr/bin/sudo -E PATH=$PATH ./get_system_info.txt > SystemInformation.txt

#Note: Please feel free to delete 'runCmd' calls to exclude the information you don't want to share

# To track if all commands in script executed successfully
return=0

### Helper Functions ###
# Print command, run command and store output in file
runCmd () {
    echo "CMD: $1"
    eval $1 2>&1

    failCode=$?
    if [ $failCode -ne 0 ]; then
        >&2 echo "Failure detected in CMD: $1"
    fi

    return+=$failCode

    echo ""
}

echo "########################################### System Info ###########################################"
echo ""


# Host OS info
runCmd 'hostnamectl'

# General OS and Kernel info
runCmd 'uname -a'

# NIC hardware information
runCmd 'lshw -c net -businfo'

# For each NIC detailed hardware info
runCmd 'for pci in $(lspci -b | grep -i eth | cut -d " " -f1); do lspci -vvs "$pci"; done'

# For each NIC driver info
runCmd 'for deviceName in $(lshw -c net -businfo | grep "@" | tr -s " " | cut -d" " -f2); do ethtool -i "$deviceName"; echo ""; done'

# For each NIC timstamping info
runCmd 'for deviceName in $(lshw -c net -businfo | grep "@" | tr -s " " | cut -d" " -f2); do ethtool -T "$deviceName"; echo ""; done'

# For each NIC show settings
runCmd 'for deviceName in $(lshw -c net -businfo | grep "@" | tr -s " " | cut -d" " -f2); do ethtool -I "$deviceName"; echo ""; done'

# For each NIC show stats
runCmd 'for deviceName in $(lshw -c net -businfo | grep "@" | tr -s " " | cut -d" " -f2); do echo "$deviceName"; ethtool -S "$deviceName"; echo ""; done'

# Accelerator info
runCmd 'lspci | grep -i acc'

# CPU info
runCmd 'lscpu'

# Kernel parameter info
runCmd 'cat /proc/cmdline'

# Memory info
runCmd 'cat /proc/meminfo'

# CPU info
runCmd 'cat /proc/cpuinfo'

# FlexRAN compiler info
runCmd 'icx --version'

# GCC compiler info
runCmd 'gcc --version'

# CPU layout info
runCmd '/home/ran/flexran/dpdk-22.11/usertools/cpu_layout.py'

# CPU layout info
runCmd 'dpdk-devbind.py -s'

# List builtin kernel modules
runCmd 'cat /lib/modules/$(uname -r)/modules.builtin'

# PTP configuration
runCmd 'ptp4l -v;phc2sys -v'

# NTP, System time info
runCmd 'systemctl status ntpd;timedatectl'

# Final step, about how it went
if [ $return -ne 0 ]; then
    echo "*** Some commands didn't executed successfully !!!"
else
    echo "############################################# SUCCESS #############################################"
fi

```
:::


### Yaml
:::spoiler viavi_oai_du
############### Common parameters #####
ORAN YANG model version: 2 #2,3,4,5,6,7 or 8  - optional: default is 2
MTU size: 9216                         #optional: default is 1500bytes
O-RU configuration mode: 0                  # 0: semi-static - 1: ODU configured - optional: default is 0
O-DU MAC address: 00:11:22:33:44:66           #only required in semi-static mode
M-plane VLAN ID: 20                         #only required in ODU configured mode
M-plane DHCP state: on                      #on/off only required in ODU configured mode
M-plane IP version: IPV4                   #IPV4/IPV6 - only required in ODU configured mode
M-plane IP address: 10.10.10.2/24        #only required in ODU configured mode and when DHCP is off
M-plane CallHome Ip address: 10.10.10.1   #only required in ODU configured mode and when DHCP is off
PTP domain number: 24                    #optional - default is 24
PTP- G_8275_1- multicast-mac-address:   #NONFORWARDABLE/FORWARDABLE - optional - default is NONFORWARDABLE

############### per CC config #####
perCC config:

- CCid: 1
  Ethernet O-RU port: ORU1-P1          #(2x2 or 4x4 or 4layerMUMIMO or 8layerMUMIMO or 16layerMUMIMO) - optional: default is 2x2
  VLAN ID: 5
  CC configuration: 2x2               #(2x2 or 4x4) - optional: default is 2x2
  Traffic type: NR                    #(LTE or NR )- optional: default is NR
  Numerology: 1                        #(0,1,3) - optional: default is 1
  Bandwidth: 100                       #(in MHz) - optional: default is 100MHz
  Alpha value: 0                       #optional: default is 0
  Beta value: 0                        #optional: default is 0
  DL Carrier Frequency: 0               #optional: default is 0
  UL Carrier Frequency: 0               #optional: default is 0
  DL compression: 9bitBFP          # (16BitUncompressed, 14bitBFP, 12bitBFP, 9bitBFP or 4bitModComp) assumes static compression controlled on a per direction basis
  DL (PDSCH) eaxcid list: 0,1        #order in list is used to control mapping on multiple BB cards
  DL mixed numerology?: no               #(yes/no) only set to yes for FR2 mixed numerology/numerology =3
  Additional SSB eaxcid list:             #only required for FR2 mixed numerology/numerology =3
  UL compression: 9bitBFP               #(16BitUncompressed, 14bitBFP, 12bitBFP or 9bitBFP) assumes static compression controlled on a per direction basis
  UL (PUSCH) eaxcid list: 0,1         #order in list could be used to control mapping on multiple BB cards
  Is PUSCH and RACH traffic on separate eaxcid?: yes #(yes/no)
  Additional RACH eaxcid list: 2,3    #only required if PUSCH and RACH are on different eaxcid
  Number of UL streams dedicated to SRS:  #only required for MU-MIMO scenarios
  Eaxcid of first SRS dedicated stream:   #subsequent eaxcid values are incremented from this initial value
:::
:::spoiler ntust_oai_du
```c=
############### Common parameters #####
ORAN YANG model version: 2 #2,3,4,5,6,7 or 8  - optional: default is 2
MTU size: 1500                         #optional: default is 1500bytes
O-RU configuration mode: 0                  # 0: semi-static - 1: ODU configured - optional: default is 0
O-DU MAC address: 00:11:22:33:44:66           #only required in semi-static mode
M-plane VLAN ID: 20                         #only required in ODU configured mode
M-plane DHCP state: on                      #on/off only required in ODU configured mode
M-plane IP version: IPV4                   #IPV4/IPV6 - only required in ODU configured mode
M-plane IP address: 10.10.10.2/24        #only required in ODU configured mode and when DHCP is off
M-plane CallHome Ip address: 10.10.10.1   #only required in ODU configured mode and when DHCP is off
PTP domain number: 24                    #optional - default is 24
PTP- G_8275_1- multicast-mac-address:   #NONFORWARDABLE/FORWARDABLE - optional - default is NONFORWARDABLE

############### per CC config #####
perCC config:

- CCid: 1
  Ethernet O-RU port: ORU1-P1          #(2x2 or 4x4 or 4layerMUMIMO or 8layerMUMIMO or 16layerMUMIMO) - optional: default is 2x2
  VLAN ID: 5
  CC configuration: 2x2               #(2x2 or 4x4) - optional: default is 2x2
  Traffic type: NR                    #(LTE or NR )- optional: default is NR
  Numerology: 1                        #(0,1,3) - optional: default is 1
  Bandwidth: 100                       #(in MHz) - optional: default is 100MHz
  Alpha value: 0                       #optional: default is 0
  Beta value: 0                        #optional: default is 0
  DL Carrier Frequency: 0               #optional: default is 0
  UL Carrier Frequency: 0               #optional: default is 0
  DL compression: 16BitUncompressed          # (16BitUncompressed, 14bitBFP, 12bitBFP, 9bitBFP or 4bitModComp) assumes static compression controlled on a per direction basis
  DL (PDSCH) eaxcid list: 0,1        #order in list is used to control mapping on multiple BB cards
  DL mixed numerology?: no               #(yes/no) only set to yes for FR2 mixed numerology/numerology =3
  Additional SSB eaxcid list:             #only required for FR2 mixed numerology/numerology =3
  UL compression: 16BitUncompressed               #(16BitUncompressed, 14bitBFP, 12bitBFP or 9bitBFP) assumes static compression controlled on a per direction basis
  UL (PUSCH) eaxcid list: 0,1         #order in list could be used to control mapping on multiple BB cards
  Is PUSCH and RACH traffic on separate eaxcid?: yes #(yes/no)
  Additional RACH eaxcid list: 2,3    #only required if PUSCH and RACH are on different eaxcid
  Number of UL streams dedicated to SRS:  #only required for MU-MIMO scenarios
  Eaxcid of first SRS dedicated stream:   #subsequent eaxcid values are incremented from this initial value
```
:::
- Common parameters
:::success
| Item                                 | NTUST             | VIAVI             |
|:------------------------------------ |:----------------- |:----------------- |
| ORAN YANG model version              | 2                 | 2                 |
| MTU size                             | 1500              | 9216              |
| O-RU configuration mode              | 0                 | 0                 |
| O-DU MAC address                     | 00:11:22:33:44:66 | 00:11:22:33:44:66 |
| M-plane VLAN ID                      | 20                | 20                |
| M-plane DHCP state                   | on                | on                |
| M-plane IP version                   | IPV4              | IPV4              |
| M-plane IP address                   | 10.10.10.2/24     | 10.10.10.2/24     |
| M-plane CallHome Ip address          | 10.10.10.1        | 10.10.10.1        |
| PTP domain number                    | 24                | 24                |
| PTP- G_8275_1- multicast-mac-address |                   |                   |
:::
::: success

| Item                                           | NTUST             | viavi   | Column 3                                                                                                                         |
|:---------------------------------------------- |:----------------- |:------- |:-------------------------------------------------------------------------------------------------------------------------------- |
| CCid:                                          | 1                 | 1       |                                                                                                                                  |
| Ethernet O-RU port:                            | ORU1-P1           | ORU1-P1 | #(2x2 or 4x4 or 4layerMUMIMO or 8layerMUMIMO or 16layerMUMIMO) - optional: default is 2x2                                        |
| VLAN ID:                                       | 5                 | 5       |                                                                                                                                  |
| CC configuration:                              | 2x2               | 2x2     | #(2x2 or 4x4) - optional: default is 2x2                                                                                         |
| Traffic type:                                  | NR                | NR      | #(LTE or NR )- optional: default is NR                                                                                           |
| Numerology:                                    | 1                 | 1       | #(0,1,3) - optional: default is 1                                                                                                |
| Bandwidth:                                     | 100               | 100     | #(in MHz) - optional: default is 100MHz                                                                                          |
| Alpha value:                                   | 0                 | 0       | #optional: default is 0                                                                                                          |
| Beta value:                                    | 0                 | 0       | #optional: default is 0                                                                                                          |
| DL Carrier Frequency:                          | 0                 | 0       | #optional: default is 0                                                                                                          |
| UL Carrier Frequency:                          | 0                 | 0       | #optional: default is 0                                                                                                          |
| DL compression:                                | 16BitUncompressed | 9bitBFP | # (16BitUncompressed, 14bitBFP, 12bitBFP, 9bitBFP or 4bitModComp) assumes static compression controlled on a per direction basis |
| DL (PDSCH) eaxcid list:                        | 0,1               | 0,1     | #order in list is used to control mapping on multiple BB cards                                                                   |
| DL mixed numerology?:                          | no                | no      | #(yes/no) only set to yes for FR2 mixed numerology/numerology =3                                                                 |
| Additional SSB eaxcid list:                    |                   |         | #only required for FR2 mixed numerology/numerology =3                                                                            |
| UL compression:                                | 16BitUncompressed | 9bitBFP | #(16BitUncompressed, 14bitBFP, 12bitBFP or 9bitBFP) assumes static compression controlled on a per direction basis               |
| UL (PUSCH) eaxcid list:                        | 0,1               | 0,1     | #order in list could be used to control mapping on multiple BB cards                                                             |
| Is PUSCH and RACH traffic on separate eaxcid?: | yes               | yes     | #(yes/no)                                                                                                                        |
| Additional RACH eaxcid list:                   | 2,3               | 2,3     | #only required if PUSCH and RACH are on different eaxcid                                                                         |
| Number of UL streams dedicated to SRS:         |                   |         | #only required for MU-MIMO scenarios                                                                                             |
| Eaxcid of first SRS dedicated stream:          |                   |         | #subsequent eaxcid values are incremented from this initial value                                                                |
:::