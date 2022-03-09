# utl-reduce-your-sdtm-and-adam-mapping-effort-using-a-common-meta-data-model
Reduce your sdtm and adam mapping effort using a common meta data model
    %let pgm=utl-reduce-your-sdtm-and-adam-mapping-effort-using-a-common-meta-data-model;

    /************************************************************************************************************************************************/
    /*                                                                                                                                              */
    *;  %let pgm=clx_010mta;                                                                                                                        *;
    /*                                                                                                                                              */
    /*  Program location: d:/clx/sas/clx_010mta.sas                                                                                                 */
    /*                                                                                                                                              */
    /*  %let purpose =Create a common meta data model to reduce the mapping efforts;                                                                */
    /*                     _                                                                                                                        */
    /*  _ __ ___  __ _  __| |_ __ ___   ___                                                                                                         */
    /* | `__/ _ \/ _` |/ _` | `_ ` _ \ / _ \                                                                                                        */
    /* | | |  __/ (_| | (_| | | | | | |  __/                                                                                                        */
    /* |_|  \___|\__,_|\__,_|_| |_| |_|\___|                                                                                                        */
    /*                                                                                                                                              */
    /*  1. Download all the externals                                                                                                               */
    /*     Create a folder for this project, I suggest d:/clx or any other drive:/clx                                                               */
    /*                                                                                                                                              */
    /*  2. Download clz.zip and extract the external inputs into the folder you created in step 1.                                                  */
    /*                                                                                                                                              */
    /*  3. Copy program c:/clx/sas/clx_010mta.sas into your editor (or use this readme or .sas version)                                             */
    /*                                                                                                                                              */
    /*  4. You should be able to run the entire program, but I suggest you highlight and run blocks of code                                         */
    /*                                                                                                                                              */
    /*  I hope I got all the dependncies on d:/clx                                                                                                  */
    /*                                                                                                                                              */
    /*  github                                                                                                                                      */
    /*  https://tinyurl.com/2p8jjrdt                                                                                                                */
    /*  https://github.com/rogerjdeangelis/utl-reduce-your-sdtm-and-adam-mapping-effort-using-a-common-meta-data-model                              */
    /*                                                                                                                                              */
    /*  Related to                                                                                                                                  */
    /*  https://tinyurl.com/2zps56xc                                                                                                                */
    /*  https://github.com/rogerjdeangelis/utl-end-to-end-cdisc-SDTM-ADaM-processing                                                                */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/
    /*                                                                                                                                              */
    /*  Description                                                                                                                                 */
    /*                                                                                                                                              */
    /*  Common Meta Data Model(CMDM) mines all previous mapping work to locate reusable mapping code, create excel meta sheeta and, make define.xml */
    /*                                                                                                                                              */
    /*  It is like the define.xml on steroids                                                                                                       */
    /*                                                                                                                                              */
    /*  Can create the excel meta workbooks and define.xml directly from the CMDM.                                                                  */
    /*                                                                                                                                              */
    /*  This is a simplified CMDM, the full model can do things like identify unmapped raw variables. Also has all the promary keys for all tables. */
    /*                                                                                                                                              */
    /*  Full CMDM has Dimension tables with CRF, SAP, PROTOCOL, SDTM IG, SDTM Guidance, SDTM contrlled terminology                                  */
    /*                                                                                                                                              */
    /*  I have some AI to do thinks like capture mappings from annotated CRFs                                                                       */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/
    /*       _                           _                 _                                                                                        */
    /*    __| | ___ _ __   ___ _ __   __| | ___ _ __   ___(_) ___  ___                                                                              */
    /*   / _` |/ _ \ `_ \ / _ \ `_ \ / _` |/ _ \ `_ \ / __| |/ _ \/ __|                                                                             */
    /*  | (_| |  __/ |_) |  __/ | | | (_| |  __/ | | | (__| |  __/\__ \                                                                             */
    /*   \__,_|\___| .__/ \___|_| |_|\__,_|\___|_| |_|\___|_|\___||___/                                                                             */
    /*             |_|                                                                                                                              */
    /*                                                                                                                                              */
    /*   Win 10 pro workstation 64bit                                                                                                               */
    /*   SAS 9.4 M7 64bit                                                                                                                           */
    /*                                                                                                                                              */
    /*   EXTERNAL MACROS ALL OF THESE ARE IN AUTOCALL FOLDER D:/CLS/OTO                                                                             */
    /*   ===============================================================                                                                            */
    /*                                                                                                                                              */
    /*   utlnopts - turn options off to minimize the log                                                                                            */
    /*   utlopts  - turn options on for debugginf                                                                                                   */
    /*                                                                                                                                              */
    /*   utl_optlenpos - seet valable length to the max observed                                                                                    */
    /*                                                                                                                                              */
    /*   utl_dirlst    - create sas data set will all files in a folder                                                                             */
    /*                                                                                                                                              */
    /*   ods_off      - turn ods off                                                                                                                */
    /*   ods_on       - turn ods on                                                                                                                 */
    /*                                                                                                                                              */
    /*   stop_submission - gently stops SAS processing                                                                                              */                                            */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /*   INTERNAL MACROS ALL OF THESE ARE IN AUTOCALL FOLDER D:/CLS/OTO                                                                             */
    /*   ===============================================================                                                                            */
    /*                                                                                                                                              */
    /*    NONE                                                                                                                                      */
    /*                                                                                                                                              */
    /*   VERSION FOLDER                                                                                                                             */
    /*                                                                                                                                              */
    /*   c:/ver/clx_010mta#timestamp*                                                                                                               */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/
    /*                                                                                                                                              */
    /*   _                   _                          _                                                         _       _       _                 */
    /*  (_)_ __  _ __  _   _| |_   _ __  _ __ _____   _(_) ___  _   _ ___    _ __ ___   __ _ _ __  _ __   ___  __| |   __| | __ _| |_ __ _          */
    /*  | | `_ \| `_ \| | | | __| | `_ \| `__/ _ \ \ / / |/ _ \| | | / __|  | `_ ` _ \ / _` | `_ \| `_ \ / _ \/ _` |  / _` |/ _` | __/ _` |         */
    /*  | | | | | |_) | |_| | |_  | |_) | | |  __/\ V /| | (_) | |_| \__ \  | | | | | | (_| | |_) | |_) |  __/ (_| | | (_| | (_| | || (_| |         */
    /*  |_|_| |_| .__/ \__,_|\__| | .__/|_|  \___| \_/ |_|\___/ \__,_|___/  |_| |_| |_|\__,_| .__/| .__/ \___|\__,_|  \__,_|\__,_|\__\__,_|         */
    /*          |_|               |_|                                                       |_|   |_|                                               */
    /*             _                        _   _                   _                                                                               */
    /*    _____  _| |_ ___ _ __ _ __   __ _| | (_)_ __  _ __  _   _| |_ ___                                             _               _           */
    /*   / _ \ \/ / __/ _ \ `__| `_ \ / _` | | | | `_ \| `_ \| | | | __/ __|                                 ___  _   _| |_ _ __  _   _| |_ ___     */
    /*  |  __/>  <| ||  __/ |  | | | | (_| | | | | | | | |_) | |_| | |_\__ \                                / _ \| | | | __| `_ \| | | | __/ __|    */
    /*   \___/_/\_\\__\___|_|  |_| |_|\__,_|_| |_|_| |_| .__/ \__,_|\__|___/                               | (_) | |_| | |_| |_) | |_| | |_\__ \    */
    /*                                                 |_|                                                  \___/ \__,_|\__| .__/ \__,_|\__|___/    */
    /*                                                                                                                     |_|                      */
    /*  d:/clx                                                                                                                                      */
    /*  |                                                                                                                                           */
    /*  +---fmt                                                                                                                                     */
    /*  |       c01raw_rawfmt.sas7bcat * C01  COVID Adult Vacine Study Formats                                                                      */
    /*  |       c02raw_rawfmt.sas7bcat * C02  COVID Pedriatic Study    Formats           D:/CLX/CLX_010MTA120 (Central DMDM fact table)             */
    /*  |                                                                                                                                           */
    /*  +---oto                        * Tools SAS autocall folder                        Variable   Label                                          */
    /*  |       array.sas                                                                                                                           */
    /*  |       arraydelete.sas                                                           SRC        SAS CRF DDT SDM DMP - Source for meta data     */
    /*  |       .......                                                                   STUDY      Study Identifier                               */
    /*  |       utl_ymrlan100.sas                                                         TABLE      Clinical Dataset CRT/SDTM/ADAM                 */
    /*  |       xpy.sas                                                                   VARIABLE   Table SAS Dataset/CRF Module..                 */
    /*  |                                                                                 QUESTION   Question ie Label, Number of Obs and Max Min   */
    /*  +---sas                        * Past programs to Map SDTMS                       ANSWER     Answer ie number of obs, location of library.. */
    /*  |      c01sdm_Ae.sas           *  C01  COVID Adult Vacine Study Formats                                 /                                   */
    /*  |      c01sdm_Dm.sas                                                                                   /                                    */
    /*  |      c01sdm_Ex.sas                                                                                  /                                     */
    /*  |      c01sdm_Lb.sas                                                                                 /                                      */
    /*  |      c01sdm_Xp.sas                                                                                /                                       */
    /*  |                                                                                                  /                                        */
    /*  |      c02sdm_Ae.sas                                                                              /                                         */
    /*  |      c02sdm_Dm.sas                                                                             /                                          */
    /*  |      c02sdm_Ex.sas                                                                            /                                           */
    /*  |      c02sdm_Lb.sas                                                                           /                                            */
    /*  |      c02sdm_Xp.sas                                                                          /                                             */
    /*  +---sd1                                                                                      /                                              */
    /*         c01raw_rawadverse.sas7bdat      * Raw Client COVID Adult Study                       /                                               */
    /*         c01raw_rawdemographic.sas7bdat                                                      /                                                */
    /*         c01raw_rawdosing.sas7bdat                                                          /                                                 */
    /*         c01raw_rawlabs.sas7bdat                                                  ______   /                                                  */
    /*         c01raw_rawpain.sas7bdat                                                 /       \                                                    */
    /*                                                                         ------>|  ROOT   |                                                   */
    /*         c02raw_rawadverse.sas7bdat      * Raw Client COVID Child Study         |         |                                                   */
    /*         c02raw_rawdemographic.sas7bdat                                         | PROCES  |                                                   */
    /*         c02raw_rawdosing.sas7bdat                                               \______ / \                                                  */
    /*         c02raw_rawlabs.sas7bdat                                                            \                                                 */
    /*         c02raw_rawpain.sas7bdat                                                             \                                                */
    /*                                                                                              -->  d:/clx/clx_010mta130sas.sas7bdat           */
    /*         c01sdm_ae.sas7bdat              * Previously mapped SDTMs                                                                            */
    /*         c01sdm_dm.sas7bdat                                                                         Variable                                  */
    /*         c01sdm_ex.sas7bdat                                                                                                                   */
    /*         c01sdm_lb.sas7bdat                                                                         SDTM_PROGRAM    Program Location          */
    /*         c01sdm_xp.sas7bdat                                                                         LINE_NUMBER     Program Line Number       */
    /*                                                                                                    SAS_STATEMENT   SAS Statemete             */
    /*         c02sdm_ae.sas7bdat                                                                                                                   */
    /*         c02sdm_dm.sas7bdat                                                                                                                   */
    /*         c02sdm_ex.sas7bdat                                                                                                                   */
    /*         c02sdm_lb.sas7bdat                                                                                                                   */
    /*         c02sdm_xp.sas7bdat                                                                                                                   */
    /*                                                                                                                                              */
    /*         c01adm_adae.sas7bdat            * Previously mapped ADaMs                                                                            */
    /*         c01adm_adef.sas7bdat                                                                                                                 */
    /*         c01adm_adsl.sas7bdat                                                                                                                 */
    /*         c01adm_adte.sas7bdat                                                                                                                 */
    /*         c02adm_adae.sas7bdat                                                                                                                 */
    /*         c02adm_adef.sas7bdat                                                                                                                 */
    /*         c02adm_adsl.sas7bdat                                                                                                                 */
    /*         c02adm_adte.sas7bdat                                                                                                                 */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/
    /*               _               _                                                                                                              */
    /*    ___  _   _| |_ _ __  _   _| |_                                                                                                            */
    /*   / _ \| | | | __| `_ \| | | | __|                                                                                                           */
    /*  | (_) | |_| | |_| |_) | |_| | |_                                                                                                            */
    /*   \___/ \__,_|\__| .__/ \__,_|\__|                                                                                                           */
    /*                  |_|                                                                                                                         */
    /*                                                                                                                                              */
    /*   Two Tables (so far for the star/snowflake schema)                                                                                          */
    /*                                                                                                                                              */
    /*   d:/clx/clx_010mta120.sas7bdat     The meta data similar to OHDSI/OMOP normalized question/answer table.                                    */
    /*   d:/clx/clx_010mta130sas.sas7bdat  All the SDTM and ADaM mapping programs programs                                                          */
    /*                                                                                                                                              */
    /*   Contents of d:/clx/clx_010mta120.sas7bdat (can easily be hundreds of thousands os records)                                                 */
    /*                                                                                                                                              */
    /*   Variables in Creation Order                                                                                                                */
    /*                                                                                                                                              */
    /*   #    Variable    Type     Len    Label                                                                                                     */
    /*                                                                                                                                              */
    /*   1    SRC         Char      16    SAS CRF DDT SDM DMP - Source for meta data                                                                */
    /*   2    STUDY       Char      32    Study Identifier                                                                                          */
    /*   3    TABLE       Char      32    Clinical Dataset CRT/SDTM/ADAM                                                                            */
    /*   4    VARIABLE    Char      32    Table SAS Dataset/CRF Module..                                                                            */
    /*   5    QUESTION    Char      32    Question ie Label, Number of Obs and Max Min                                                              */
    /*   6    ANSWER      Char    8192    Answer ie number of obs, location of library..                                                            */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /*   Contents of d:/clx/clx_010mta130sas.sas7bdat                                                                                               */
    /*                                                                                                                                              */
    /*     Variables in Creation Order                                                                                                              */
    /*                                                                                                                                              */
    /*   #    Variable         Type   Len                                                                                                           */
    /*                                                                                                                                              */
    /*   1    SDTM_PROGRAM     Char    96   Program Location                                                                                        */
    /*   2    LINE_NUMBER      Num      8   Program Line Number                                                                                     */
    /*   3    SAS_STATEMENT    Char   384   SAS Statemete                                                                                           */
    /*                            _                           _                                        _                                            */
    /*  ___  __ _ _ __ ___  _ __ | | ___    ___ _ __ ___   __| |_ __ ___    _ __ ___  ___ ___  _ __ __| |___                                        */
    /* / __|/ _` | `_ ` _ \| `_ \| |/ _ \  / __| `_ ` _ \ / _` | `_ ` _ \  | `__/ _ \/ __/ _ \| `__/ _` / __|                                       */
    /* \__ \ (_| | | | | | | |_) | |  __/ | (__| | | | | | (_| | | | | | | | | |  __/ (_| (_) | | | (_| \__ \                                       */
    /* |___/\__,_|_| |_| |_| .__/|_|\___|  \___|_| |_| |_|\__,_|_| |_| |_| |_|  \___|\___\___/|_|  \__,_|___/                                       */
    /*                     |_|                                                                                                                      */
    /*                                                                                                                                              */
    /*  MAXIMUM and MINIMUM AGE                                                                                                                     */
    /*  =======================                                                                                                                     */
    /*                                                                                                                                              */
    /*   SDTM AGE for C01 Study, COVID Adults Study and C02 Study, COVID Pediatric Study                                                            */
    /*                                                                                                                                              */
    /*    Obs       SRC        STUDY       TABLE      VARIABLE     QUESTION      ANSWER                                                             */
    /*                                                                                                                                              */
    /*   5260    CLX_SCHEMA    C01SDM    C01SDM_DM      AGE       SDM_MIN_MAX    28@70    C01 Study, COVID Adults Study                             */
    /*   5390    CLX_SCHEMA    C02SDM    C02SDM_DM      AGE       SDM_MIN_MAX     5@17    C02 Study, COVID Pediatric Study                          */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /*  LAB TESTS                                                                                                                                   */
    /*  =========                                                                                                                                   */
    /*                                                                                                                                              */
    /*   All the SDTM Lab Tests in both Studies ( ALBUMIN=30 Lab Test then frequency, @ is delimiter)                                               */
    /*                                                                                                                                              */
    /*          SRC        STUDY       TABLE      VARIABLE     QUESTION                                                                             */
    /*                                                                                                                                              */
    /* 7217  CLX_SCHEMA    C02SDM    C02SDM_LB     LBTEST     SDM_FREQUENCIES                                                                       */
    /*                                                                                                                                              */
    /*  Obs                                                                ANSWER                                                                   */
    /*                                                                                                                                              */
    /* 7217  ALBUMIN=30@ALK. PHOS.=30@ALT (SGPT)=30@AST (SGOT)=30@DIRECT BILI=30@GGTP=30@HEMATOCRIT=30@HEMOGLOBIN=30@TOTAL BILI=30@TOTAL PROT=30    */
    /*                                                                                                                                              */
    /*    LBTEST        COUNT                                                                                                                       */
    /*                                                                                                                                              */
    /*    ALBUMIN         30                                                                                                                        */
    /*    ALK. PHOS.      30                                                                                                                        */
    /*    ALT (SGPT)      30                                                                                                                        */
    /*    AST (SGOT)      30                                                                                                                        */
    /*    DIRECT BILI     30                                                                                                                        */
    /*    GGTP            30                                                                                                                        */
    /*    HEMATOCRIT      30                                                                                                                        */
    /*    HEMOGLOBIN      30                                                                                                                        */
    /*    TOTAL BILI      30                                                                                                                        */
    /*    TOTAL PROT      30                                                                                                                        */
    /*                                                                                                                                              */
    /* MAPPING DOSE                                                                                                                                 */
    /* ============                                                                                                                                 */
    /*                                                                                                                                              */
    /*  Querying d:/clx/clx_010mta120sas for the mapping of SDTM variable EXDOSE                                                                    */
    /*                                                                                                                                              */
    /*                                                                      LINE_                                                                   */
    /*   Obs          SDTM_PROGRAM              SAS_STATEMENT              NUMBER                                                                   */
    /*                                                                                                                                              */
    /*   312    D:\CLX\SAS\C01SDM_EX.SAS        exdose = dailydose;          13                                                                     */
    /*                                                                                                                                              */
    /*                     _              __                                      _                                                                 */
    /*   ___ _ __ ___   __| |_ __ ___    / _|_ __ ___  __ _ _   _  ___ _ __   ___(_) ___  ___                                                       */
    /*  / __| `_ ` _ \ / _` | `_ ` _ \  | |_| `__/ _ \/ _` | | | |/ _ \ `_ \ / __| |/ _ \/ __|                                                      */
    /* | (__| | | | | | (_| | | | | | | |  _| | |  __/ (_| | |_| |  __/ | | | (__| |  __/\__ \                                                      */
    /*  \___|_| |_| |_|\__,_|_| |_| |_| |_| |_|  \___|\__, |\__,_|\___|_| |_|\___|_|\___||___/                                                      */
    /*                                                   |_|                                                                                        */
    /*                                                                                                                                              */
    /*   COVID SCHEMA ( could add C03 study in the future)                                                                                          */
    /*                                                                                                                                              */
    /*   SAS CRF DDT SDM DMP - Source for meta data                                                                                                 */
    /*                                                                                                                                              */
    /*                                          Cumulative    Cumulative                                                                            */
    /*   SRC           Frequency     Percent     Frequency      Percent                                                                             */
    /*   ---------------------------------------------------------------                                                                            */
    /*   CLX_SCHEMA        8494      100.00          8494       100.00                                                                              */
    /*                                                                                                                                              */
    /*   There two COVID studies                                                                                                                    */
    /*                                                                                                                                              */
    /*   STUDY     Frequency                                                                                                                        */
    /*   --------------------                                                                                                                       */
    /*   C01RAW        1439    Raw Data  C01 Study, COVID Adults Study                                                                                */
    /*   C02RAW        1439    Raw Data  C02 Study, COVID Pediatric Study                                                                             */
    /*                                                                                                                                              */
    /*   C01SDM        2189    SDTMs                                                                                                                */
    /*   C02SDM        2189                                                                                                                         */
    /*                                                                                                                                              */
    /*   C01SDM        2189    ADaM                                                                                                                 */
    /*   C02SDM        2189                                                                                                                         */
    /*                                                                                                                                              */
    /*   TABLE               Frequency                                                                                                              */
    /*   ------------------------------                                                                                                             */
    /*   C01ADM_ADAE       ADaM   172                                                                                                               */
    /*   C01ADM_ADEF              172                                                                                                               */
    /*                                                                                                                                              */
    /*   C01RAW_RAWDOSING  RAW    114                                                                                                               */
    /*   C01RAW_RAWLABS           254                                                                                                               */
    /*                                                                                                                                              */
    /*   C01SDM_AE         SDTM   395                                                                                                               */
    /*   C01SDM_DM                375                                                                                                               */
    /*                                                                                                                                              */
    /*   SAMPLE                                                                                                                                           */
    /*   VARIABLE             Frequency                                                                                                             */
    /*   -------------------------------                                                                                                            */
    /*   ***TABLE_LEVEL***         122                                                                                                              */
    /*   @0N1YF                      4                                                                                                              */
    /*   $ELGR1NF                    4                                                                                                              */
    /*   AEBODSYS                  110                                                                                                              */
    /*   AEDECOD                   110                                                                                                              */
    /*   AVAL                       48                                                                                                              */
    /*   AVALC                       4                                                                                                              */
    /*   AVISIT                      4                                                                                                              */
    /*   BASE                       24                                                                                                              */
    /*   BODYSYS                    58                                                                                                              */
    /*   BRTHDT                     24                                                                                                              */
    /*   BRTHDTC                    48                                                                                                              */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /*   RAW QUESTIONS            SDM QUESTIONS               ADM QUESTIONS                                                                         */
    /*   =============            ===============             =============                                                                         */
    /*                                                                                                                                              */
    /*   RAW_CV                    SDM_CV                      ADM_CV                                                                               */
    /*   RAW_DISTINCT              SDM_DISTINCT                ADM_DISTINCT                                                                         */
    /*   RAW_FILESIZE              SDM_FILESIZE                ADM_FILESIZE                                                                         */
    /*   RAW_FORMAT                SDM_FORMAT                  ADM_FORMAT                                                                           */
    /*   RAW_FREQUENCIES           SDM_FREQUENCIES             ADM_FREQUENCIES                                                                      */
    /*   RAW_INFORMAT              SDM_INFORMAT                ADM_INFORMAT                                                                         */
    /*   RAW_LABEL                 SDM_LABEL                   ADM_LABEL                                                                            */
    /*   RAW_LENGTH                SDM_LENGTH                  ADM_LENGTH                                                                           */
    /*   RAW_LEVELS_N_MISS_NONMISS SDM_LEVELS_N_MISS_NONMISS   ADM_LEVELS_N_MISS_NONMISS                                                            */
    /*   RAW_MAX                   SDM_MAX                     ADM_MAX                                                                              */
    /*   RAW_MEAN                  SDM_MEAN                    ADM_MEAN                                                                             */
    /*   RAW_MEDIAN                SDM_MEDIAN                  ADM_MEDIAN                                                                           */
    /*   RAW_MIN                   SDM_MIN                     ADM_MIN                                                                              */
    /*   RAW_MIN_MAX               SDM_MIN_MAX                 ADM_MIN_MAX                                                                          */
    /*   RAW_MIS_POP               SDM_MIS_POP                 ADM_MIS_POP                                                                          */
    /*   RAW_N                     SDM_N                       ADM_N                                                                                */
    /*   RAW_NMISS                 SDM_NMISS                   ADM_NMISS                                                                            */
    /*   RAW_NUMBER_VARIABLES      SDM_NUMBER_VARIABLES        ADM_NUMBER_VARIABLES                                                                 */
    /*   RAW_OBSERVATIONS          SDM_OBSERVATIONS            ADM_OBSERVATIONS                                                                     */
    /*   RAW_Q1                    SDM_Q1                      ADM_Q1                                                                               */
    /*   RAW_Q3                    SDM_Q3                      ADM_Q3                                                                               */
    /*   RAW_RECORD_LENGTH         SDM_RECORD_LENGTH           ADM_RECORD_LENGTH                                                                    */
    /*   RAW_STD                   SDM_STD                     ADM_STD                                                                              */
    /*   RAW_TYPE                  SDM_TYPE                    ADM_TYPE                                                                             */
    /*   RAW_USER_FORMAT           SDM_PROGRAM                 ADM_PROGRAM                                                                          */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /*   SAMPLE ANSWERS                                                                                                                             */
    /*   ==============                                                                                                                             */
    /*                                                                                                                                              */
    /*      SRC        STUDY             TABLE        VARIABLE    QUESTION                           ANSWER                                         */
    /*                                                                                                                                              */
    /*   CLX_SCHEMA    C01ADM    C01ADM_ADAE          USUBJID             ADM_DISTINCT               20                                             */
    /*   CLX_SCHEMA    C01ADM    C01ADM_ADAE          AGE                 ADM_MIN_MAX                29@70                                          */
    /*   CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE    AETEXT              RAW_LENGTH                 26                                             */
    /*   CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE    AEREL               RAW_LABEL                  RELATEDNESS: 1=NOT,2=POSSIBLY,3=PROBABLY       */
    /*   CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE    AEREL               RAW_TYPE                   CHAR                                           */
    /*   CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE    AEREL               RAW_LENGTH                 8                                              */
    /*   CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE    AESEV               RAW_LABEL                  SEVERITY/INTENSITY:1=MILD,2=MODERATE,3=SEVERE  */
    /*   CLX_SCHEMA    C01ADM    C01ADM_ADAE          AENDY               ADM_LEVELS_N_MISS_NONMISS  27@1@26                                        */
    /*   CLX_SCHEMA    C01ADM    C01ADM_AE            ***TABLE_LEVEL***   ADM_PROGRAM                D:/CLX/SAS/C01ADM_AE.SAS                       */
    /*   CLX_SCHEMA    C02SDM    C02SDM_LB            LBNRIND             SDM_FREQUENCIES            NORMAL=248@HIGH=43@LOW=9                       */
    /*   CLX_SCHEMA    C01ADM    C01ADM_ADAE          AENDT               ADM_MIN_MAX                18308@18547                                    */
    /*   CLX_SCHEMA    C02SDM    C02SDM_DM            RACE                SDM_FREQUENCIES            White=36@Black=18@Other=6                      */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/
    */                                         _                                                                                                    */
    */   ___  __ _ ___    __ _ _   _  ___ _ __(_) ___  ___                                                                                          */
    */  / __|/ _` / __|  / _` | | | |/ _ \ `__| |/ _ \/ __|                                                                                         */
    */  \__ \ (_| \__ \ | (_| | |_| |  __/ |  | |  __/\__ \                                                                                         */
    */  |___/\__,_|___/  \__, |\__,_|\___|_|  |_|\___||___/                                                                                         */
    */                      |_|                                                                                                                     */
    */                                                                                                                                              */
    */   How many LAB tests are in the all of our COVID Studies                                                                                     */
    */                                                                                                                                              */
    */   libname clx "d:/clx";                                                                                                                      */
    */                                                                                                                                              */
    */   proc sql;                                                                                                                                  */
    */     create                                                                                                                                   */
    */       table lbtest as                                                                                                                        */
    */     select                                                                                                                                   */
    */       distinct                                                                                                                               */
    */         table                                                                                                                                */
    */        ,question                                                                                                                             */
    */        ,answer                                                                                                                               */
    */     from                                                                                                                                     */
    */        clx.clx_010mta120                                                                                                                     */
    */     where                                                                                                                                    */
    */        variable = "LBTEST"                                                                                                                   */
    */        and question ="SDM_FREQUENCIES"                                                                                                       */
    */    ;quit;                                                                                                                                    */
    */                                                                                                                                              */
    */    data nrm(keep=table lbtest count);                                                                                                        */
    */      set lbtest;                                                                                                                             */
    */      do idx=1 to countc(answer,'@');                                                                                                         */
    */        txt=scan(answer,idx,'@');                                                                                                             */
    */        lbtest=scan(txt,1,'=');                                                                                                               */
    */        count =scan(txt,2,'=');                                                                                                               */
    */        output;                                                                                                                               */
    */      end;                                                                                                                                    */
    */    run;quit;                                                                                                                                 */
    */                                                                                                                                              */
    */      TABLE      LBTEST         COUNT                                                                                                         */
    */                                                                                                                                              */
    */    C01SDM_LB    ALBUMIN         30                                                                                                           */
    */    C01SDM_LB    ALK. PHOS.      30                                                                                                           */
    */    C01SDM_LB    ALT (SGPT)      30                                                                                                           */
    */    C01SDM_LB    AST (SGOT)      30                                                                                                           */
    */    C01SDM_LB    DIRECT BILI     30                                                                                                           */
    */    C01SDM_LB    GGTP            30                                                                                                           */
    */    C01SDM_LB    HEMATOCRIT      30                                                                                                           */
    */    C01SDM_LB    HEMOGLOBIN      30                                                                                                           */
    */    C01SDM_LB    TOTAL BILI      30                                                                                                           */
    */                                                                                                                                              */
    */    C02SDM_LB    ALBUMIN         30                                                                                                           */
    */    C02SDM_LB    ALK. PHOS.      30                                                                                                           */
    */    C02SDM_LB    ALT (SGPT)      30                                                                                                           */
    */    C02SDM_LB    AST (SGOT)      30                                                                                                           */
    */    C02SDM_LB    DIRECT BILI     30                                                                                                           */
    */    C02SDM_LB    GGTP            30                                                                                                           */
    */    C02SDM_LB    HEMATOCRIT      30                                                                                                           */
    */    C02SDM_LB    HEMOGLOBIN      30                                                                                                           */
    */    C02SDM_LB    TOTAL BILI      30                                                                                                           */
    */                                                                                                                                              */
    */                                                                                                                                              */
    */                                                                                                                                              */
    */    ALL REFERENCES TO SDM VARIABLE EXDOSE IN ALL PROGRAMS                                                                                     */
    */                                                                                                                                              */
    */    proc sql;                                                                                                                                 */
    */       select                                                                                                                                 */
    */           *                                                                                                                                  */
    */       from                                                                                                                                   */
    */           clx.clx_010mta130sas                                                                                                               */
    */       where                                                                                                                                  */
    */           upcase(sas_statement) contains "EXDOSE"                                                                                            */
    */    ;quit;                                                                                                                                    */
    */                                                                                                                                              */
    */                                                                                                                                              */
    */    SDTM_PROGRAM               SAS_STATEMENT                                                                LINE_NUMBER                       */
    */    -------------------------------------------------------------------------------------------------------------------                       */
    */    D:\CLX\SAS\C01SDM_EX.SAS   exdose = dailydose;                                                               13                           */
    */    D:\CLX\SAS\C01SDM_EX.SAS   retain STUDYID DOMAIN USUBJID EXSEQ EXTRT EXDOSE EXDOSU EXDOSFRM EXDOSTOT         49                           */
    */    D:\CLX\SAS\C01SDM_EX.SAS   EXDOSE                 N8       2                   Dose per Administration       99                           */
    */                                                                                                                                              */
    */    D:\CLX\SAS\C02SDM_EX.SAS   exdose = dailydose;                                                               13                           */
    */    D:\CLX\SAS\C02SDM_EX.SAS   retain STUDYID DOMAIN USUBJID EXSEQ EXTRT EXDOSE EXDOSU EXDOSFRM EXDOSTOT         49                           */
    */    D:\CLX\SAS\C02SDM_EX.SAS   EXDOSE                 N8       2                   Dose per Administration       99                           */
    */                                                                                                                                              */
    */                                                                                                                                              */
    */    HOW IS SEX MAPPED                                                                                                                         */
    */                                                                                                                                              */
    */    proc sql;                                                                                                                                 */
    */       select                                                                                                                                 */
    */           *                                                                                                                                  */
    */       from                                                                                                                                   */
    */           clx.clx_010mta130sas                                                                                                               */
    */       where                                                                                                                                  */
    */           upcase(sas_statement) contains "SEX"                                                                                               */
    */    ;quit;                                                                                                                                    */
    */                                                                                                                                              */
    */    D:\CLX\SAS\C02SDM_DM.SAS          sex=gender;                                                                                             */
    */                                                                                                                                              */
    */    SDTM_PROGRAM                      SAS_STATEMENT  LINE_NUMBER                                                                              */
    */    ------------------------------------------------------------                                                                              */
    */    D:\CLX\SAS\C02SDM_DM.SAS          sex=gender;       123                                                                                   */
    */                                                                                                                                              */
    /************************************************************************************************************************************************/
    /*                                                                                                                                              */
    /*   _ __  _ __ ___   ___ ___  ___ ___                                                                                                          */
    /*  | `_ \| `__/ _ \ / __/ _ \/ __/ __|                                                                                                         */
    /*  | |_) | | | (_) | (_|  __/\__ \__ \                                                                                                         */
    /*  | .__/|_|  \___/ \___\___||___/___/                                                                                                         */
    /*  |_|                                                                                                                                         */
    /*                                                                                                                                              */
    /*                                                                                                                                              */
    /*   1. Download 7zip file with all the external inputs from github                                                                             */
    /*      Unzip into your root directory. I used d:/clx for the COVID studies                                                                     */
    /*                                                                                                                                              */
    /*   2. Clear all datasets in the work directory                                                                                                */
    /*      Clear macro arrays (this is especially useful f you are testing interactively and need to rerun)                                        */
    /*                                                                                                                                              */
    /*   INIT                                                                                                                                       */
    /*                                                                                                                                              */
    /*     1. Download 7zip file with all the external inputs from github                                                                           */
    /*        Unzip into your root directory. I used d:/clx for the COVID studies                                                                   */
    /*                                                                                                                                              */
    /*     2. Clear all datasets in the work directory                                                                                              */
    /*        Clear macro arrays (this is especially useful f you are testing interactively and need to rerun)                                      */
    /*                                                                                                                                              */
    /*     3. Create attribute template for CMDM (these are the only variables that will be in the CMDM fact table                                  */
    /*                                                                                                                                              */
    /*         %let atr=%str(                                                                                                                       */
    /*         length                                                                                                                               */
    /*            Src           $16      /* SAS CRF DDT SDM DMP - Source for meta data */                                                           */
    /*            Study         $32                                                                                                                 */
    /*            Table         $32                                                                                                                 */
    /*            Variable      $32                                                                                                                 */
    /*            Question      $32     /* question see below for some of the questions */                                                          */
    /*            Answer        $8192;                                                                                                              */
    /*         label                                                                                                                                */
    /*            Src      = "SAS CRF DDT SDM DMP - Source for meta data"                                                                           */
    /*            Study    = "Study Identifier"                                                                                                     */
    /*            Table    = "Clinical Dataset CRT/SDTM/ADAM"                                                                                       */
    /*            Variable = "Table SAS Dataset/CRF Module.."                                                                                       */
    /*            Question = "Question ie Label, Number of Obs and Max Min"                                                                         */
    /*            Answer   = "Answer ie number of obs, location of library..";                                                                      */
    /*          keep                                                                                                                                */
    /*            Src                                                                                                                               */
    /*            Study                                                                                                                             */
    /*            Table                                                                                                                             */
    /*            Variable                                                                                                                          */
    /*            Question                                                                                                                          */
    /*            Answer;);                                                                                                                         */
    /*                                                                                                                                              */
    /*     4. The external folder &root/sd1 has all of the raw datasets, mapped sdtm datasets and the adam datasets                                 */
    /*        Create a a temp dataset with all of the datasets names                                                                                */
    /*                                                                                                                                              */
    /*     5. Assign a libname to the folder with all the external input table                                                                      */
    /*                                                                                                                                              */
    /*     6. Query the sas sas dictionary.vcolum to get type, length, labels and formats for all                                                   */
    /*        variables in all of the tables                                                                                                        */
    /*                                                                                                                                              */
    /*   MACRO ARRAYs                                                                                                                               */
    /*                                                                                                                                              */
    /*     7. Create macro array _jusTbls, with our 28 external tables in ur studies.                                                               */
    /*                                                                                                                                              */
    /*          _justbl1  ie C01SDM_AE                                                                                                              */
    /*          _justbl2                                                                                                                            */
    /*          _justbl3                                                                                                                            */
    /*          ..                                                                                                                                  */
    /*          _justbl28                                                                                                                           */
    /*                                                                                                                                              */
    /*          and                                                                                                                                 */
    /*          _justbln (which has the value 28)                                                                                                   */
    /*                                                                                                                                              */
    /*      8. Create these additional macro arrays                                                                                                 */
    /*                                                                                                                                              */
    /*          _tbl#      all tables                                                                                                               */
    /*          _var#      all vars                                                                                                                 */
    /*                                                                                                                                              */
    /*          _tblchr#   tables with char vars                                                                                                    */
    /*          _varchr#   char vars in tables                                                                                                      */
    /*                                                                                                                                              */
    /*          _tblnum#   tables with numeric vars                                                                                                 */
    /*          _varnum#   numeric vars in tables                                                                                                   */
    /*                                                                                                                                              */
    /*    POPULATE ATTRIBUTE TYPE, LENGTH ....                                                                                                      */
    /*                                                                                                                                              */
    /*      9. Populate attribute template with data from all tables and all variables for these questions                                          */
    /*                                                                                                                                              */
    /*           RAW_LABEL       SDM_LABEL      ADM_LABEL                                                                                           */
    /*           RAW_FORMAT      SDM_FORMAT     ADM_FORMAT                                                                                          */
    /*           RAW_INFORMAT    SDM_INFORMAT   ADM_INFORMAT                                                                                        */
    /*           RAW_TYPE        SDM_TYPE       ADM_TYPE                                                                                            */
    /*           RAW_LENGTH      SDM_LENGTH     ADM_LENGTH                                                                                          */
    /*                                                                                                                                              */
    /*      10.  Populate N-Levels N-MissLevels N-NonMissLevels for all variables                                                                   */
    /*                                                                                                                                              */
    /*      11.  Populate MIN_MAX for all variables                                                                                                 */
    /*                                                                                                                                              */
    /*      12.  Populate frequency of Missing and Non-missing for all variables                                                                    */
    /*                                                                                                                                              */
    /*      13.  Populate table level statistics for all tables                                                                                     */
    /*                                                                                                                                              */
    /*             OBSERVATIONS                                                                                                                     */
    /*             FILESIZE                                                                                                                         */
    /*             NUMBER_VARIABLES                                                                                                                 */
    /*             RECORD_LENGTH                                                                                                                    */
    /*                                                                                                                                              */
    /*      14.  Populate all formats                                                                                                               */
    /*                                                                                                                                              */
    /*      15.  Populate staistics for all numeric variables                                                                                       */
    /*           n nmiss min q1 median mean std q3 cv max                                                                                           */
    /*                                                                                                                                              */
    /*      16.  Populate count distinct for all variables                                                                                          */
    /*                                                                                                                                              */
    /*      17.  Populate the top 30 most frequent values for all variables                                                                         */
    /*                                                                                                                                              */
    /*      18.  Populate  clx_010mta130sas wirh all the programs                                                                                   */
    /*                                                                                                                                              */
    /*              Variables in Creation Order                                                                                                     */
    /*                                                                                                                                              */
    /*           #    Variable         Type    Len                                                                                                  */
    /*                                                                                                                                              */
    /*           1    SDTM_PROGRAM     Char     24                                                                                                  */
    /*           2    SAS_STATEMENT    Char    113                                                                                                  */
    /*           3    LINE_NUMBER      Num       3                                                                                                  */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/
    /*                                                                                                                                              */
    /* CHANGE HISTORY                                                                                                                               */
    /*                                                                                                                                              */
    /*      Date          Programmer                Comments  Phone         Address                                                                 */
    /*                                                                                                                                              */
    /*  1.  03/09/2022    rdeangelis@gmail.com      Creation  301 655 9489  5349 N Fort Yuma Trail Tucson AZ 85750                                  */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/

    %*stop_submission;

    /*       _ _
    (_)_ __ (_) |_
    | | `_ \| | __|
    | | | | | | |_
    |_|_| |_|_|\__|

    */

    %let pgm=clx_mta;

    * incase you accidentally run the entire code - you need to rum this a section at a time;

    %let root=d:/clx;

    libname clx    "&root";
    libname clxsd1 "&root/sd1";
    libname clxfmt "&root/fmt";

    * clear the work directory;
    proc datasets lib=work kill nodetails nolist mt=data;
    run;quit;

    * delete interim datasets if you rerun;
    proc datasets lib=clx nodetails nolist;
      delete
          clx_010mta010
          clx_020mta010
          clx_030mta010
          clx_040mta010
          clx_050mta010
          clx_060mta010
          clx_070mta010
          clx_080mta010
          clx_090mta010
          clx_100mta010
          clx_110mta010
          clx_120mta010
          clx_130mtasas
       ;
    run;quit;

    * delete macro aarrays;
    %utlnopts;
    %arraydelete(_tbl   )
    %arraydelete(_var   )
    %arraydelete(_tblchr)
    %arraydelete(_varchr)
    %arraydelete(_tblnum)
    %arraydelete(_varnum)
    %utlopts;

    /*
     _                       _       _
    | |_ ___ _ __ ___  _ __ | | __ _| |_ ___
    | __/ _ \ `_ ` _ \| `_ \| |/ _` | __/ _ \
    | ||  __/ | | | | | |_) | | (_| | ||  __/
     \__\___|_| |_| |_| .__/|_|\__,_|\__\___|
                      |_|
    */

    * Structure of Commom Meta data Model;
    * asking question of the meta data;

    %let atr=%str(
    length
       Src           $16      /* SAS CRF DDT SDM DMP - Source for meta data */
       Study         $32
       Table         $32
       Variable      $32
       Question      $32     /* question see below for some of the questions */
       Answer        $8192;
    label
       Src      = "SAS CRF DDT SDM DMP - Source for meta data"
       Study    = "Study Identifier"
       Table    = "Clinical Dataset CRT/SDTM/ADAM"
       Variable = "Table SAS Dataset/CRF Module.."
       Question = "Question ie Label, Number of Obs and Max Min"
       Answer   = "Answer ie number of obs, location of library..";
     keep
       Src
       Study
       Table
       Variable
       Question
       Answer;);


    /*           _   _
      ___  _ __ | |_(_) ___  _ __  ___
     / _ \| `_ \| __| |/ _ \| `_ \/ __|
    | (_) | |_) | |_| | (_) | | | \__ \
     \___/| .__/ \__|_|\___/|_| |_|___/
          |_|
    */

    options sasautos=("&root/oto" ) compress=binary validvarname=upcase ls=255;

    /*_  __    _    ____ ____   ___       _    ____  ____      _ __   ______
    |  \/  |  / \  / ___|  _ \ / _ \     / \  |  _ \|  _ \    / \\ \ / / ___|
    | |\/| | / _ \| |   | |_) | | | |   / _ \ | |_) | |_) |  / _ \\ V /\___ \
    | |  | |/ ___ \ |___|  _ <| |_| |  / ___ \|  _ <|  _ <  / ___ \| |  ___) |
    |_|  |_/_/   \_\____|_| \_\\___/  /_/   \_\_| \_\_| \_\/_/   \_\_| |____/
                                          _        _     _
     _ __ __ ___      __  ___  __ _ ___  | |_ __ _| |__ | | ___  ___
    | `__/ _` \ \ /\ / / / __|/ _` / __| | __/ _` | `_ \| |/ _ \/ __|
    | | | (_| |\ V  V /  \__ \ (_| \__ \ | || (_| | |_) | |  __/\__ \
    |_|  \__,_| \_/\_/   |___/\__,_|___/  \__\__,_|_.__/|_|\___||___/

    */

    * these arrays wiil be used over and over again;

    %utl_dirlst(&root/sd1,clx_010mtaSd1);

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Up to 40 obs WORK.CLX_010MTASD1 total obs=28 07MAR2022:15:18:2                                                        */
    /*                                                                                                                        */
    /*     FILENAME                            FILENAME                FILENAME                                               */
    /*                                                                                                                        */
    /*     RAW date to Map                     SDTMs                   ADAM                                                   */
    /*                                                                                                                        */
    /*     c01raw_rawadverse.sas7bdat          c01sdm_ae.sas7bdat      c01adm_adae.sas7bdat                                   */
    /*     c01raw_rawdemographic.sas7bdat      c01sdm_dm.sas7bdat      c01adm_adef.sas7bdat                                   */
    /*     c01raw_rawdosing.sas7bdat           c01sdm_ex.sas7bdat      c01adm_adsl.sas7bdat                                   */
    /*     c01raw_rawlabs.sas7bdat             c01sdm_lb.sas7bdat      c01adm_adte.sas7bdat                                   */
    /*     c01raw_rawpain.sas7bdat             c01sdm_xp.sas7bdat      c02adm_adae.sas7bdat                                   */
    /*     c02raw_rawadverse.sas7bdat          c02sdm_ae.sas7bdat      c02adm_adef.sas7bdat                                   */
    /*     c02raw_rawdemographic.sas7bdat      c02sdm_dm.sas7bdat      c02adm_adsl.sas7bdat                                   */
    /*     c02raw_rawdosing.sas7bdat           c02sdm_ex.sas7bdat      c02adm_adte.sas7bdat                                   */
    /*     c02raw_rawlabs.sas7bdat             c02sdm_lb.sas7bdat                                                             */
    /*     c02raw_rawpain.sas7bdat             c02sdm_xp.sas7bdat                                                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    data clx_010mtaLst;
      length sd1 $32;
      set clx_010mtaSd1;
      if index(filename,'sas7bdat')>0;
      sd1=upcase(cats(scan(filename,1,"."))) ;
      keep sd1;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Up to 40 obs WORK.CLX_010MTALST total obs=28 07MAR2022:15:21:46                                                       */
    /*                                                                                                                        */
    /*  SD1                            SD1       AD1                                                                          */
    /*                                                                                                                        */
    /*  RAW date to Map               SDTMs      ADAMs                                                                        */
    /*                                                                                                                        */
    /*  c01raw_rawadverse          c01sdm_ae     c01adm_adae                                                                  */
    /*  c01raw_rawdemographic      c01sdm_dm     c01adm_adef                                                                  */
    /*  c01raw_rawdosing           c01sdm_ex     c01adm_adsl                                                                  */
    /*  c01raw_rawlabs             c01sdm_lb     c01adm_adte                                                                  */
    /*  c01raw_rawpain             c01sdm_xp     c02adm_adae                                                                  */
    /*  c02raw_rawadverse          c02sdm_ae     c02adm_adef                                                                  */
    /*  c02raw_rawdemographic      c02sdm_dm     c02adm_adsl                                                                  */
    /*  c02raw_rawdosing           c02sdm_ex     c02adm_adte                                                                  */
    /*  c02raw_rawlabs             c02sdm_lb                                                                                  */
    /*  c02raw_rawpain             c02sdm_xp                                                                                  */
    /*                                                                                                                        */
    /***************************************************************************************************************************

    /*
     _ __ ___   __ _  ___ _ __ ___     __ _ _ __ _ __ __ _ _   _ ___
    | `_ ` _ \ / _` |/ __| `__/ _ \   / _` | `__| `__/ _` | | | / __|
    | | | | | | (_| | (__| | | (_) | | (_| | |  | | | (_| | |_| \__ \
    |_| |_| |_|\__,_|\___|_|  \___/   \__,_|_|  |_|  \__,_|\__, |___/
                              _ _      _   _               |___/
     _ __ __ ___      __   __| (_) ___| |_(_) ___  _ __   __ _ _ __ _   _
    | `__/ _` \ \ /\ / /  / _` | |/ __| __| |/ _ \| `_ \ / _` | `__| | | |
    | | | (_| |\ V  V /  | (_| | | (__| |_| | (_) | | | | (_| | |  | |_| |
    |_|  \__,_| \_/\_/    \__,_|_|\___|\__|_|\___/|_| |_|\__,_|_|   \__, |
                                                                    |___/
    */

    * bootstrap from meta data;

    proc sql;
     create
           table clx.clx_010mta010  as
     select
           'CLX_SCHEMA'                 as Src      length 32
          ,memname                      as Table    length=32
          ,name                         as Variable length=32
          ,upcase(coalesce(label,name)) as label    length=8192
          ,scan(memname,1,'_')          as study    length=32
          ,coalesce(format,"NULL")      as format   length=8192
          ,coalesce(informat,"NULL")    as informat length=8192
          ,upcase(type)                 as type     length=8192
          ,length
     from
           sashelp.vcolumn
     where
          libname="CLXSD1"
    ;quit;

    /************************************************************************************************************************************************/
    /*                                                                                                                                              */
    /*Up to 40 obs from CLX.CLX_010MTA010 total obs=462 09MAR2022:09:22:49                                                                          */
    /*                                                                                                                                              */
    /*Obs       SRC           TABLE       VARIABLE    LABEL                                  STUDY     FORMAT       INFORMAT    TYPE    LENGTH      */
    /*                                                                                                                                              */
    /*  1    CLX_SCHEMA    C01ADM_ADAE    STUDYID     STUDY IDENTIFIER                       C01ADM    NULL           NULL      CHAR       15       */
    /*  2    CLX_SCHEMA    C01ADM_ADAE    USUBJID     UNIQUE SUBJECT IDENTIFIER              C01ADM    NULL           NULL      CHAR       25       */
    /*  3    CLX_SCHEMA    C01ADM_ADAE    SITEID      STUDY SITE IDENTIFIER                  C01ADM    NULL           NULL      CHAR        7       */
    /*  4    CLX_SCHEMA    C01ADM_ADAE    COUNTRY     COUNTRY                                C01ADM    NULL           NULL      CHAR        3       */
    /*  5    CLX_SCHEMA    C01ADM_ADAE    AESEQ       SEQUENCE NUMBER                        C01ADM    NULL           NULL      NUM         8       */
    /*  6    CLX_SCHEMA    C01ADM_ADAE    AGE         UNIQUE SUBJECT IDENTIFIER              C01ADM    NULL           NULL      NUM         8       */
    /* ...                                                                                                                                          */
    /************************************************************************************************************************************************/

    /* _           _     _        _     _
      (_)_   _ ___| |_  | |_ __ _| |__ | | ___  ___
      | | | | / __| __| | __/ _` | `_ \| |/ _ \/ __|
      | | |_| \__ \ |_  | || (_| | |_) | |  __/\__ \
     _/ |\__,_|___/\__|  \__\__,_|_.__/|_|\___||___/
    |__/
    */

    proc sql noprint;
      *reset inobs=3;
      select
         distinct
          Table
      into
          :_jusTbl1-
      from
          clx.clx_010mta010
    ;quit;

    %let _jusTbln=&sqlobs;

    %let _endtbl=&&_jusTbl&_jusTbln;

    %utlnopts;
    %put &=_jusTbln    Table for each variable;
    %put &=_jusTbl1    First table in macro array;
    %put &=_endTbl     Last table in macro array;
    %utlopts;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* _JUSTBLN=28                   Tables                                                                                   */
    /*                                                                                                                        */
    /* _JUSTBL1=C01RAW_RAWADVERSE    First table in macro array                                                               */
    /* _ENDTBL=C02SDM_XP             Last table in macro array                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     __   ____ _ _ __ ___
     \ \ / / _` | `__/ __|
      \ V / (_| | |  \__ \
       \_/ \__,_|_|  |___/

    */

    proc sql noprint;
      *reset inobs=3;
      select
          Table
         ,Variable
      into
          :_tbl1-
         ,:_var1-
      from
          clx.clx_010mta010
    ;quit;

    %let _tbln=&sqlobs;
    %let _varn=&sqlobs;

    %let _endtbl=&&_tbl&_tbln;
    %let _endvar=&&_var&_varn;

    %utlnopts;
    %put &=_tbln    Table for each variable;
    %put &=_varn    Number of Variables;
    %put &=_tbl1    First table in macro array;
    %put &=_endtbl     Last table in macro array;
    %put &=_var1    First Variable in macro array;
    %put &=_endvar     Last variable in macro array;
    %utlopts;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*    MACRO ARRAY RAW VARIABLES WITH 462 VARIABLE NAMES AND ASSOCIATED TABLE NAMES                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*    _TBLN=462                  Tables for each variable                                                                 */
    /*    _VARN=462                  Number of Variables                                                                      */
    /*    _TBL1=C01RAW_RAWADVERSE    First table in macro array                                                               */
    /*    _ENDTBL=C02SDM_XP          Last table in macro array                                                                */
    /*    _VAR1=SUBJECT              First Variable in macro array                                                            */
    /*    _ENDVAR=XPSTRESN           Last variable in macro array                                                             */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*      _
       ___| |__   __ _ _ __  __   ____ _ _ __ ___
      / __| `_ \ / _` | `__| \ \ / / _` | `__/ __|
     | (__| | | | (_| | |     \ V / (_| | |  \__ \
      \___|_| |_|\__,_|_|      \_/ \__,_|_|  |___/

    */

    proc sql noprint;
      *reset inobs=3;
      select
          table
         ,Variable
      into
          :_tblchr1-
         ,:_varchr1-
      from
          clx.clx_010mta010
      where
          upcase(type)="CHAR"
    ;quit;

    %let _tblchrn=&sqlobs;
    %let _varchrn=&sqlobs;

    %let _endtbl=&&_tblchr&_tblchrn;
    %let _endvar=&&_varchr&_varchrn;

    %utlnopts;
    %put &=_tblchrn    Table for each char ble;
    %put &=_varchrn    Number of Char Variables;
    %put &=_tblchr1    First table in char macro array;
    %put &=_endtbl     Last table in char macro array;
    %put &=_varchr1    First char Variable in macro array;
    %put &=_endvar     Last chr variable in macro array;
    %utlopts;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* _TBLCHRN=292           Tables for each char ble                                                                        */
    /* _VARCHRN=292           Number of Char Variables                                                                        */
    /*                                                                                                                        */
    /* _TBLCHR1=RAWADVERSE    First table in char macro array                                                                 */
    /* _ENDTBL=C02SDM_XP      Last table in char macro array                                                                  */
    /*                                                                                                                        */
    /* _VARCHR1=BODYSYS       First char Variable in macro array                                                              */
    /* _ENDVAR=XPSTRESC       Last chr variable in macro array                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
      _ __  _   _ _ __ ___   __   ____ _ _ __ ___
     | `_ \| | | | `_ ` _ \  \ \ / / _` | `__/ __|
     | | | | |_| | | | | | |  \ V / (_| | |  \__ \
     |_| |_|\__,_|_| |_| |_|   \_/ \__,_|_|  |___/

    */

    proc sql noprint;
      *reset inobs=3;
      select
          table
         ,Variable
      into
          :_tblnum1-
         ,:_varnum1-
      from
          clx.clx_010mta010
      where
          upcase(type)="NUM"
    ;quit;

    %let _tblnumn=&sqlobs;
    %let _varnumn=&sqlobs;

    %let _endtbl=&&_tblnum&_tblnumn;
    %let _endvar=&&_varnum&_varnumn;

    %utlnopts;
    %put &=_tblnumn    Table for each num ble;
    %put &=_varnumn    Number of num variables;
    %put &=_tblnum1    First table in num macro array;
    %put &=_endtbl     Last table in num macro array;
    %put &=_varnum1    First num variable in macro array;
    %put &=_endvar     Last num variable in macro array;
    %utlopts;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* _TBLNUMN=170            Table for each num ble                                                                         */
    /* _VARNUMN=170            Number of num variables                                                                        */
    /*                                                                                                                        */
    /* _TBLNUM1=RAWADVERSE    First table in num macro array                                                                  */
    /* _ENDTBL=C02SDM_XP      Last table in num macro array                                                                   */
    /*                                                                                                                        */
    /* _VARNUM1=SUBJECT       First num variable in macro array                                                               */
    /* _ENDVAR=XPSTRESN       Last num variable in macro array                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Available macro arrays                                                                                                 */
    /*                                                                                                                        */
    /*       _jusTbl#                                                                                                         */
    /*                                                                                                                        */
    /*       _tbl#      all tables and vars                                                                                   */
    /*       _var#                                                                                                            */
    /*                                                                                                                        */
    /*       _tblchr#   tables with char vars                                                                                 */
    /*       _varchr#   char vars in tables                                                                                   */
    /*                                                                                                                        */
    /*       _tblnum#   tables with char vars                                                                                 */
    /*       _varnum#   char vars in tables                                                                                   */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |   __ _ _ __ _ __ __ _ _   _ ___
     / _ \ `_ \ / _` |  / _` | `__| `__/ _` | | | / __|
    |  __/ | | | (_| | | (_| | |  | | | (_| | |_| \__ \
     \___|_| |_|\__,_|  \__,_|_|  |_|  \__,_|\__, |___/
         _             _                    _ |___/
     ___| |_ __ _ _ __| |_   _ __ ___   ___| |_ __ _
    / __| __/ _` | `__| __| | `_ ` _ \ / _ \ __/ _` |
    \__ \ || (_| | |  | |_  | | | | | |  __/ || (_| |
    |___/\__\__,_|_|   \__| |_| |_| |_|\___|\__\__,_|

    */

    * add new meta data;

    data clx.clx_010mta020  ;

     &atr;

       set clx.clx_010mta010 end=fin;

       Src='CLX_SCHEMA';

       Question=cats(substr(study,4),"_LABEL"   );  Answer=label                    ;output;
       Question=cats(substr(study,4),"_FORMAT"  );  Answer=format                   ;output;
       Question=cats(substr(study,4),"_INFORMAT");  Answer=informat                 ;output;
       Question=cats(substr(study,4),"_TYPE"    );  Answer=upcase(type)             ;output;
       Question=cats(substr(study,4),"_LENGTH"  );  Answer=strip(put(length,??7.))  ;output;

    run;quit;

    /*************************************************************************************************************************/
    /*                                                                                                                       */
    /*  Up to 40 obs CLX.CLX_010MTA020 total obs=2,310 07MAR2022:18:36:12                                                    */
    /*                                                                                                                       */
    /*   Obs      SRC       STUDY          TABLE         VARIABLE   QUESTION       ANSWER                                    */
    /*                                                                                                                       */
    /*     1   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   SUBJECT    RAW_LABEL      SUBJECT NUMBER                            */
    /*     2   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   SUBJECT    RAW_FORMAT     BEST.                                     */
    /*     3   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   SUBJECT    RAW_INFORMAT   NULL                                      */
    /*     4   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   SUBJECT    RAW_TYPE       NUM                                       */
    /*     5   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   SUBJECT    RAW_LENGTH     8                                         */
    /*     6   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   _AEREL     RAW_LABEL      B                                         */
    /*     7   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   _AEREL     RAW_FORMAT     BEST.                                     */
    /*     8   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   _AEREL     RAW_INFORMAT   NULL                                      */
    /*     9   CXX_SCHEMA   C01RAW   C01RAW_RAWADVERSE   _AEREL     RAW_TYPE       NUM                                       */
    /*                                                                                                                       */
    /*************************************************************************************************************************/
    /*
     _                _
    | | _____   _____| |___
    | |/ _ \ \ / / _ \ / __|
    | |  __/\ V /  __/ \__ \
    |_|\___| \_/ \___|_|___/

    */

    proc datasets lib=work nodetails nolist;
      delete clx_010MtaTpl;
    run;quit;

    data clx_010MtaTpl;
      length Study $32 Table  $32 Variable  $32 NLevels NMissLevels NNonMissLevels 8 ;
      call missing(of _all_);
      stop;
    run;

    %utlnopts;

    %odsoff;

    %do_over(_tbl,phrase=%str(

         *ods select nlevels;
         ods output nlevels=?all;
         proc freq nlevels data=CLXSD1.?;
         tables _all_ /missing;

         proc datasets lib=work nodetails nolist;
            delete clx_010MtaTplAdd;
         run;quit;

         data clx_010MtaTplAdd;
           length study Table Variable $32;
           retain Table "?";
           study=substr("?",1,6);
           set ?all(rename=TABLEVAR=Variable);
           Table=upcase("?");
         run;quit;

         proc datasets lib=clxSd1 nodetails nolist;
           delete ?all;
         run;quit;

         proc append base= clx_010MtaTpl data=clx_010MtaTplAdd(drop=tablevarlabel);
         run;quit;
       ));

    %odson;

    %utlopts;

    /************************************************************************************************************************************************/
    /*                                                                                                                                              */
    /*  Up to 40 obs from CLX_010MTATPL total obs=9,058 09MAR2022:13:27:12                                                                          */
    /*                                                                                                                                              */
    /*   Obs    STUDY        TABLE       VARIABLE    NLEVELS    NMISSLEVELS    NNONMISSLEVELS                                                       */
    /*                                                                                                                                              */
    /*     1    C01ADM    C01ADM_ADAE    STUDYID         1           0                1                                                             */
    /*     2    C01ADM    C01ADM_ADAE    USUBJID        20           0               20                                                             */
    /*     3    C01ADM    C01ADM_ADAE    SITEID          5           0                5                                                             */
    /*     4    C01ADM    C01ADM_ADAE    COUNTRY         1           0                1                                                             */
    /*     5    C01ADM    C01ADM_ADAE    AESEQ           3           0                3                                                             */
    /*     6    C01ADM    C01ADM_ADAE    AGE            15           0               15                                                             */
    /*     7    C01ADM    C01ADM_ADAE    AGEGR1N         2           0                2                                                             */
    /*     8    C01ADM    C01ADM_ADAE    AGEGR1          2           0                2                                                             */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/

    /* add new levels to meta data */
    data clx.clx_010mta030 ;
      &atr;
     do until(fin);
      set clx.clx_010mta020  end=fin;
      output;
     end;
     do until (dne);
      set clx_010MtaTpl end=dne;
        Src='CLX_SCHEMA';
        Table=cats(Study,'_',substr(Table,8));
        Question=cats(substr(study,4),'_LEVELS_N_MISS_NONMISS');
        Answer=cats(put(nlevels,12.),'@',put(nmisslevels,12.),'@',put(nnonmisslevels,12.));
        output;
     end;
     stop;
    run;

    /************************************************************************************************************************************************/
    /*                                                                                                                                              */
    /*   last 41 obs from CLX.CLX_010MTA030 total obs=11,368 07MAR2022:19:05:28                                                                     */
    /*                                                                                                                                              */
    /*    Obs       SRC        STUDY       TABLE      VARIABLE            QUESTION             ANSWER                                               */
    /*                                                                                                                                              */
    /*   5144    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSTRESC    SDM_LEVELS_N_MISS_NONMISS    4@0@4                                                */
    /*   5145    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSTRESN    SDM_LEVELS_N_MISS_NONMISS    4@0@4                                                */
    /*   5146    CLX_SCHEMA    C02SDM    C02SDM_XP    STUDYID     SDM_LEVELS_N_MISS_NONMISS    1@0@1                                                */
    /*   5147    CLX_SCHEMA    C02SDM    C02SDM_XP    DOMAIN      SDM_LEVELS_N_MISS_NONMISS    1@0@1                                                */
    /*   5148    CLX_SCHEMA    C02SDM    C02SDM_XP    USUBJID     SDM_LEVELS_N_MISS_NONMISS    60@0@60                                              */
    /*   5149    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_LEVELS_N_MISS_NONMISS    3@0@3                                                */
    /*   5150    CLX_SCHEMA    C02SDM    C02SDM_XP    XPTESTCD    SDM_LEVELS_N_MISS_NONMISS    1@0@1                                                */
    /*   5151    CLX_SCHEMA    C02SDM    C02SDM_XP    XPTEST      SDM_LEVELS_N_MISS_NONMISS    1@0@1                                                */
    /*                                                                                                                                              */
    /************************************************************************************************************************************************/

    /*         _
     _ __ ___ (_)_ __    _ __ ___   __ ___  __
    | `_ ` _ \| | `_ \  | `_ ` _ \ / _` \ \/ /
    | | | | | | | | | | | | | | | | (_| |>  <
    |_| |_| |_|_|_| |_| |_| |_| |_|\__,_/_/\_\

    */

    * initialize for append;

    proc datasets lib=work nodetails nolist;
      delete clx_010mta040 maxmin;
    run;quit;

    %utlnopts;

    %do_over(_tbl _var, phrase=%str(

        * so you do not add the previous one;

        proc datasets lib=work nolist nodetails;
          delete  clx_010mta040One;
        run;quit;

        proc sql;
          create
              table clx_010mta040One as
          select
             'CLS_SCHEMA'                           as src
            ,scan("?_tbl",1,'_')                    as study
            ,"?_tbl"                                as table    length=32
            ,"?_var"                                as variable length=32
            ,cats(substr("?_tbl",4,3),"_MIN_MAX")   as question length=32
            ,catx("@",min(?_var), max(?_var) )      as answer   length=8192
          from
             CLXSD1.?_tbl
       ;quit;

        proc append base=clx_010mta040 data=clx_010mta040One;
        run;quit;

    ));

    %utlopts;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*Up to 40 obs from CLX_010MTA040 total obs=462 09MAR2022:13:29:54                                                        */
    /*                                                                                                                        */
    /*Obs       SRC        STUDY        TABLE       VARIABLE     QUESTION      ANSWER                                         */
    /*                                                                                                                        */
    /*  1    CLS_SCHEMA    C01ADM    C01ADM_ADAE    STUDYID     ADM_MIN_MAX    XYZ123@XYZ123                                  */
    /*  2    CLS_SCHEMA    C01ADM    C01ADM_ADAE    USUBJID     ADM_MIN_MAX    UNI101@UNI507                                  */
    /*  3    CLS_SCHEMA    C01ADM    C01ADM_ADAE    SITEID      ADM_MIN_MAX    100@500                                        */
    /*  4    CLS_SCHEMA    C01ADM    C01ADM_ADAE    COUNTRY     ADM_MIN_MAX    USA@USA                                        */
    /*  5    CLS_SCHEMA    C01ADM    C01ADM_ADAE    AESEQ       ADM_MIN_MAX    1@3                                            */
    /*  6    CLS_SCHEMA    C01ADM    C01ADM_ADAE    AGE         ADM_MIN_MAX    29@70                                          */
    /*  7    CLS_SCHEMA    C01ADM    C01ADM_ADAE    AGEGR1N     ADM_MIN_MAX    1@2                                            */
    /*  8    CLS_SCHEMA    C01ADM    C01ADM_ADAE    AGEGR1      ADM_MIN_MAX    <55 YEARS@>=55 YEARS                           */
    /*  9    CLS_SCHEMA    C01ADM    C01ADM_ADAE    SEX         ADM_MIN_MAX    B@W                                            */
    /* 10    CLS_SCHEMA    C01ADM    C01ADM_ADAE    TRTAN       ADM_MIN_MAX    0@0                                            */
    /* 11    CLS_SCHEMA    C01ADM    C01ADM_ADAE    TRTA        ADM_MIN_MAX    Active@Placebo                                 */
    /* 12    CLS_SCHEMA    C01ADM    C01ADM_ADAE    AETERM      ADM_MIN_MAX    ABDOMINAL CRAMPS@URINARY BL                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    * add maxmin;

    data clx.clx_010mta050 ;
      &atr;
     do until(fin);
      set clx.clx_010mta030  end=fin;
      output;
     end;
     do until (dne);
      set clx_010mta040 end=dne;
        Src='CLX_SCHEMA';
        output;
     end;
     stop;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  last 41 obs fromCLX.CLX_010MTA040 total obs=11,830 08MAR2022:06:50:54                                                 */
    /*                                                                                                                        */
    /*   Obs       SRC        STUDY       TABLE      VARIABLE     QUESTION      ANSWER                                        */
    /*                                                                                                                        */
    /*  5404    RAW_SCHEMA    C02SDM    C02SDM_EX    EXDOSFRM    SDM_MIN_MAX    TABLET, COATED@TABLET, COATED                 */
    /*  5405    RAW_SCHEMA    C02SDM    C02SDM_EX    EXDOSTOT    SDM_MIN_MAX    1@3                                           */
    /*  5406    RAW_SCHEMA    C02SDM    C02SDM_EX    EXSTDTC     SDM_MIN_MAX    2010-01-01@2010-10-02                         */
    /*  5407    RAW_SCHEMA    C02SDM    C02SDM_EX    EXENDTC     SDM_MIN_MAX    2010-01-05@2010-12-27                         */
    /*  5408    RAW_SCHEMA    C02SDM    C02SDM_EX    EXSTDY      SDM_MIN_MAX    1@253                                         */
    /*  5409    RAW_SCHEMA    C02SDM    C02SDM_EX    EXENDY      SDM_MIN_MAX    4@335                                         */
    /*  5410    RAW_SCHEMA    C02SDM    C02SDM_LB    STUDYID     SDM_MIN_MAX    XYZ123@XYZ123                                 */
    /*  5411    RAW_SCHEMA    C02SDM    C02SDM_LB    DOMAIN      SDM_MIN_MAX    LB@LB                                         */
    /*  5412    RAW_SCHEMA    C02SDM    C02SDM_LB    USUBJID     SDM_MIN_MAX    UNI101@UNI204                                 */
    /*  5413    RAW_SCHEMA    C02SDM    C02SDM_LB    LBSEQ       SDM_MIN_MAX    1@30                                          */
    /*  5414    RAW_SCHEMA    C02SDM    C02SDM_LB    LBTESTCD    SDM_MIN_MAX    ALBUMIN@TOTAL PR                              */
    /*  5415    RAW_SCHEMA    C02SDM    C02SDM_LB    LBTEST      SDM_MIN_MAX    ALBUMIN@TOTAL PROT                            */
    /*  5416    RAW_SCHEMA    C02SDM    C02SDM_LB    LBCAT       SDM_MIN_MAX    CHEMISTRY@HEMATOLOGY                          */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                                                  _
      _ __   ___  _ __    _ __ ___ (_)___
     | `_ \ / _ \| `_ \  | `_ ` _ \| / __|
     | |_) | (_) | |_) | | | | | | | \__ \
     | .__/ \___/| .__/  |_| |_| |_|_|___/
     |_|         |_|
    */

    proc format;

      value num2mis
       . = 'MIS'
       other='POP'
       ;

       value $chr2mis
       'Unknown',' ','UNK','U','NA','UNKNOWN','Missing','MISSING','MISS' ='MIS'
       other='POP'
        ;
    run;

    %utlnopts;

    %odsoff;

    %do_over(_justbl, phrase=%str(
        ods output onewayfreqs=?_a;
        proc freq data=CLXSD1.?;
          format _character_ $chr2mis. _numeric_ num2mis.;
          tables _all_/missing;
        run;quit;
    ));

    %odson;

    /*******************************************************************************************************************************************************************/
    /*                                                                                                                                                                 */
    /* Up to 40 obs WORK.C02SDM_XP_A total obs=16 08MAR2022:06:55:28                                                                                                   */
    /*                                                                                                                                                                 */
    /* Obs      TABLE       F_STUDYID  STUDYID  FREQUENCY  PERCENT  CUMFREQUENCY  CUMPERCENT  F_DOMAIN  DOMAIN  F_USUBJID  USUBJID  F_XPSEQ  XPSEQ  F_XPTESTCD         */
    /*                                                                                                                                                                 */
    /*   1  Table STUDYID      POP     XYZ123      177     100.000       177        100.000                                                    .                       */
    /*   2  Table DOMAIN                           177     100.000       177        100.000     POP       XP                                   .                       */
    /*   3  Table USUBJID                          177     100.000       177        100.000                        POP     UNI101              .                       */
    /*   4  Table XPSEQ                            177     100.000       177        100.000                                           POS      1                       */
    /*   5  Table XPTESTCD                         177     100.000       177        100.000                                                    .       POP             */
    /*   6  Table XPTEST                           177     100.000       177        100.000                                                    .                       */
    /*   7  Table XPORRES                          177     100.000       177        100.000                                                    .                       */
    /*   8  Table VISITNUM                          60      33.898        60         33.898                                                    .                       */
    /*                                                                                                                                                                 */
    /*******************************************************************************************************************************************************************/

    * initialize for append;
    proc datasets lib=work nodetails nolist;
      delete xpopmis;
    run;quit;

    %utlnopts;

    %do_over(_justbl, phrase=%str(
    data mpofix ;
       &atr;

       set ?_a;

       study=scan("?",1,'_');
       src='CLX_SCHEMA';
       question=cats(substr("?",4,3),'_MIS_POP');
       Tablex="?" ;
       Variablex =  cats('F_',scan( table , 2 , ' '));
       value    = vvaluex(Variablex) ;
       select (value);
         when ('MIS') answer=cats('MIS@',put(frequency,8.));
         when ('POP') answer=cats('POP@',put(frequency,8.));
       end;
       variable=substr(variablex,3);
       table=tablex;
    run;

    proc datasets lib=work nodetails nolist;
      delete ?_a;
    run;quit;

    proc append data=mpofix base=xpopmis ;
    run;quit;
    ));

    %utlopts;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  last 41 obs from XPOPMIS total obs=510 08MAR2022:07:54:13                                                             */
    /*                                                                                                                        */
    /*  Obs       SRC        STUDY       TABLE      VARIABLE     QUESTION      ANSWER                                         */
    /*                                                                                                                        */
    /*  236    CLX_SCHEMA    C02SDM    C02SDM_EX    EXDOSTOT    SDM_MIS_POP    POP@84                                         */
    /*  237    CLX_SCHEMA    C02SDM    C02SDM_EX    EXSTDTC     SDM_MIS_POP    POP@84                                         */
    /*  238    CLX_SCHEMA    C02SDM    C02SDM_EX    EXENDTC     SDM_MIS_POP    POP@84                                         */
    /*  239    CLX_SCHEMA    C02SDM    C02SDM_EX    EXSTDY      SDM_MIS_POP    POP@84                                         */
    /*  240    CLX_SCHEMA    C02SDM    C02SDM_EX    EXENDY      SDM_MIS_POP    POP@84                                         */
    /*  241    CLX_SCHEMA    C02SDM    C02SDM_LB    STUDYID     SDM_MIS_POP    POP@300                                        */
    /*  242    CLX_SCHEMA    C02SDM    C02SDM_LB    DOMAIN      SDM_MIS_POP    POP@300                                        */
    /*  243    CLX_SCHEMA    C02SDM    C02SDM_LB    USUBJID     SDM_MIS_POP    POP@300                                        */
    /*  244    CLX_SCHEMA    C02SDM    C02SDM_LB    LBSEQ       SDM_MIS_POP    POP@300                                        */
    /*  245    CLX_SCHEMA    C02SDM    C02SDM_LB    LBTESTCD    SDM_MIS_POP    POP@300                                        */
    /*  246    CLX_SCHEMA    C02SDM    C02SDM_LB    LBTEST      SDM_MIS_POP    POP@300                                        */
    /*  ..                                                                                                                      */
    /**************************************************************************************************************************/

    proc sql;
     create
       table mispop as
     select
       coalesce(l.Src      ,r.Src      )  as Src
      ,coalesce(l.Study    ,r.Study    )  as Study
      ,coalesce(l.Table      ,r.Table  )  as Table
      ,coalesce(l.Variable ,r.Variable )  as Variable
      ,coalesce(l.Question ,r.Question )  as Question
      ,cats(Coalesce(l.Answer,'MIS@0'),'#',Coalesce(r.Answer,'POP@0') )  as Answer  length=8192
     from
      xpopmis(where=(answer =: 'MIS' )) as l full outer join xpopmis(where=(answer =: 'POP' ))  as r
     on
      l.Table=r.Table
      and l.Variable = r.Variable
    ;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Concat mising with populated                                                                                           */
    /*                                                                                                                        */
    /* Up to 40 obs WORK.MISPOP total obs=462 08MAR2022:07:57:18                                                              */
    /*                                                                                                                        */
    /* Obs       SRC        STUDY     TABLE                    VARIABLE      QUESTION      ANSWER                             */
    /*                                                                                                                        */
    /*   1    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AEACN        RAW_MIS_POP    MIS@0#POP@40                       */
    /*   2    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AEACTION     RAW_MIS_POP    MIS@0#POP@40                       */
    /*   3    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AEBODSYS     RAW_MIS_POP    MIS@0#POP@40                       */
    /*   4    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AEDECOD      RAW_MIS_POP    MIS@3#POP@40                       */
    /*   5    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AEEND        RAW_MIS_POP    MIS@8#POP@32                       */
    /*   6    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AEENDTC      RAW_MIS_POP    MIS@0#POP@40                       */
    /*   7    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AEREL        RAW_MIS_POP    MIS@0#POP@40                       */
    /*   8    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AESER        RAW_MIS_POP    MIS@0#POP@40                       */
    /*   9    CLX_SCHEMA    C01RAW    C01RAW_RAWADVERSE        AESEV        RAW_MIS_POP    MIS@3#POP@40                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    %utlopts;
    %odson;

    data clx.clx_010mta060;
      &atr;
     do until(fin);
      set clx.clx_010mta050     end=fin;
      Table=upcase(Table);
      output;
     end;
     do until (dne);
      set mispop end=dne;
        Src='CLX_SCHEMA';
        output;
     end;
     stop;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* last 41 obs from CLX.CLX_010MTA060 total obs=12,292 09MAR2022:13:46:58                                                 */
    /*                                                                                                                        */
    /*   Obs       SRC        STUDY       TABLE      VARIABLE     QUESTION          ANSWER                                    */
    /*                                                                                                                        */
    /* 12252    CLX_SCHEMA    C02SDM    C02SDM_EX    EXSEQ       SDM_MIS_POP    MIS@0#POP@84                                  */
    /* 12253    CLX_SCHEMA    C02SDM    C02SDM_EX    EXSTDTC     SDM_MIS_POP    MIS@0#POP@84                                  */
    /* 12254    CLX_SCHEMA    C02SDM    C02SDM_EX    EXSTDY      SDM_MIS_POP    MIS@0#POP@84                                  */
    /* 12255    CLX_SCHEMA    C02SDM    C02SDM_EX    EXTRT       SDM_MIS_POP    MIS@0#POP@84                                  */
    /* 12256    CLX_SCHEMA    C02SDM    C02SDM_EX    STUDYID     SDM_MIS_POP    MIS@0#POP@84                                  */
    /* 12257    CLX_SCHEMA    C02SDM    C02SDM_EX    USUBJID     SDM_MIS_POP    MIS@0#POP@84                                  */
    /* 12258    CLX_SCHEMA    C02SDM    C02SDM_LB    DOMAIN      SDM_MIS_POP    MIS@0#POP@300                                 */
    /* 12259    CLX_SCHEMA    C02SDM    C02SDM_LB    LBBLFL      SDM_MIS_POP    MIS@200#POP@100                               */
    /* 12260    CLX_SCHEMA    C02SDM    C02SDM_LB    LBCAT       SDM_MIS_POP    MIS@0#POP@300                                 */
    /* 12261    CLX_SCHEMA    C02SDM    C02SDM_LB    LBDTC       SDM_MIS_POP    MIS@0#POP@300                                 */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
      _        _     _
     | |_ __ _| |__ | | ___  ___
     | __/ _` | `_ \| |/ _ \/ __|
     | || (_| | |_) | |  __/\__ \
      \__\__,_|_.__/|_|\___||___/

    */

    data xmstable;

       &atr;
       set sashelp.vtable(where=(libname="CLXSD1" and memtype="DATA"));

       src    = "CLX_SCHEMA";
       Table =  MEMNAME;
       Variable= cats("***TABLE_LEVEL***");
       study  = scan(MEMNAME,1,'_');

       pfx=cats(substr(memname,4,3));

       question=cats(pfx,"_OBSERVATIONS"    ); answer=cats(nobs)     ; output ;
       question=cats(pfx,"_FILESIZE"        ); answer=cats(filesize) ; output ;
       question=cats(pfx,"_NUMBER_VARIABLES"); answer=cats(nvar)     ; output ;
       question=cats(pfx,"_RECORD_LENGTH"   ); answer=cats(nlobs)    ; output ;

    run;quit;

    data clx.clx_010mta070 ;
      &atr;
     do until(fin);
      set clx.clx_010mta060    end=fin;
      output;
     end;
     do until (dne);
      set xmstable end=dne;
        if answer ne "" then output;
     end;
     stop;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* last 41 obs from CLX.CLX_010MTA070 total obs=12,404 09MAR2022:13:48:24                                                 */
    /*                                                                                                                        */
    /*   Obs       SRC        STUDY     TABLE                        VARIABLE         QUESTION                ANSWER          */
    /*                                                                                                                        */
    /* 12364    CLX_SCHEMA    C02ADM    C02ADM_ADTE              ***TABLE_LEVEL***    ADM_RECORD_LENGTH       60              */
    /* 12365    CLX_SCHEMA    C02RAW    C02RAW_RAWADVERSE        ***TABLE_LEVEL***    RAW_OBSERVATIONS        40              */
    /* 12366    CLX_SCHEMA    C02RAW    C02RAW_RAWADVERSE        ***TABLE_LEVEL***    RAW_FILESIZE            131072          */
    /* 12367    CLX_SCHEMA    C02RAW    C02RAW_RAWADVERSE        ***TABLE_LEVEL***    RAW_NUMBER_VARIABLES    20              */
    /* 12368    CLX_SCHEMA    C02RAW    C02RAW_RAWADVERSE        ***TABLE_LEVEL***    RAW_RECORD_LENGTH       40              */
    /* 12369    CLX_SCHEMA    C02RAW    C02RAW_RAWDEMOGRAPHIC    ***TABLE_LEVEL***    RAW_OBSERVATIONS        60              */
    /* 12370    CLX_SCHEMA    C02RAW    C02RAW_RAWDEMOGRAPHIC    ***TABLE_LEVEL***    RAW_FILESIZE            131072          */
    /* 12371    CLX_SCHEMA    C02RAW    C02RAW_RAWDEMOGRAPHIC    ***TABLE_LEVEL***    RAW_NUMBER_VARIABLES    9               */
    /* 12372    CLX_SCHEMA    C02RAW    C02RAW_RAWDEMOGRAPHIC    ***TABLE_LEVEL***    RAW_RECORD_LENGTH       60              */
    /* 12373    CLX_SCHEMA    C02RAW    C02RAW_RAWDOSING         ***TABLE_LEVEL***    RAW_OBSERVATIONS        84              */
    /* 12374    CLX_SCHEMA    C02RAW    C02RAW_RAWDOSING         ***TABLE_LEVEL***    RAW_FILESIZE            131072          */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    /*
       __                            _
      / _| ___  _ __ _ __ ___   __ _| |_ ___
     | |_ / _ \| `__| `_ ` _ \ / _` | __/ __|
     |  _| (_) | |  | | | | | | (_| | |_\__ \
     |_|  \___/|_|  |_| |_| |_|\__,_|\__|___/

    */

    %utl_dirlst(&root/fmt,fmt);

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Up to 40 obs WORK.FMT total obs=2 08MAR2022:08:35:26                                                                   */
    /*                                                                                                                        */
    /*                                  NUMBER_OF_    MEMBER_                                                                 */
    /* Obs           FILENAME             MEMBERS      NUMBER                                                                 */
    /*                                                                                                                        */
    /*  1     co1raw_rawfmt.sas7bcat         2           1                                                                    */
    /*  2     co2raw_rawfmt.sas7bcat         2           2                                                                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    proc sql;
       select
          upcase(scan(filename,1,'.'))
       into
          :_fmt1-
       from
          fmt
    ;quit;

    %let _fmtn=&sqlobs;

    %put &_fmt1;
    %put &_fmt2;
    %put &_fmtn;

    proc datasets lib=work nodetails nolist;
      delete allFmt;
    run;quit;

    %utlnopts;

    %do_over(_fmt,phrase=%str(
       proc format cntlout=? lib=CLXFMT.?;
       run;quit;
       data ?FMT;
         &atr;
         retain answer;
         set ?;
         by fmtname notsorted;
         src="CLX_SCHEMA";
         study=scan("?",1,'_');
         if type="C" then Variable=cats("$",fmtname,'F');
         else Variable=cats("@",substr(fmtname,2),'F');;
         question="RAW_USER_FORMAT";
         Table=cats(study,"_",FMTNAME,'_FMT');
         if label="" then label="NA";
         if start="" then start="NA";
         if first.fmtname then do;
            answer=catx("@",cats(start,'#',end,'#',label));
         end;
         else answer=catx("@",answer,cats(start,'#',end,'#',label));
         if last.fmtname then do;
            output;
            answer="";
          end;
       run;quit;
       proc append data=?FMT base=allFmt;
       run;quit;
       ));

    %utlopts;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Up to 40 obs WORK.ALLFMT total obs=22 08MAR2022:09:31:29                                                               */
    /*                                                                                                                        */
    /* Obs       SRC        STUDY     TABLE                 VARIABLE      QUESTION       ANSWER                               */
    /*                                                                                                                        */
    /*   1    CLX_SCHEMA    CO1RAW    CO1RAW_AGEGR1N_FMT    @GEGR1NF   RAW_USER_FORMAT   0#54#1@55#HIGH#2                     */
    /*   2    CLX_SCHEMA    CO1RAW    CO1RAW_AGEGR1__FMT    @GEGR1_F   RAW_USER_FORMAT   1#1#<55 YEARS@2#2#>=55 YEARS         */
    /*   3    CLX_SCHEMA    CO1RAW    CO1RAW_AVISITN_FMT    @VISITNF   RAW_USER_FORMAT   1#1#3@2#2#6                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    data clx.clx_010mta080 ;
      &atr;
     do until(fin);
      set clx.clx_010mta070 end=fin;
      output;
     end;
     do until (dne);
      set allFmt end=dne;
        output;
     end;
     stop;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* last 41 obs from CLX.CLX_010MTA080 total obs=12,426 09MAR2022:13:50:20                                                 */
    /*                                                                                                                        */
    /*  Obs     SRC      STUDY   TABLE               VARIABLE           QUESTION               ANSWER                         */
    /*                                                                                                                        */
    /* 5782  CLX_SCHEMA  C02SDM  C02SDM_XP           ***TABLE_LEVEL***  SDM_FILESIZE           262144                         */
    /* 5783  CLX_SCHEMA  C02SDM  C02SDM_XP           ***TABLE_LEVEL***  SDM_NUMBER_VARIABLES   13                             */
    /* 5784  CLX_SCHEMA  C02SDM  C02SDM_XP           ***TABLE_LEVEL***  SDM_RECORD_LENGTH      177                            */
    /* 5785  CLX_SCHEMA  CO1RAW  CO1RAW_AGEGR1N_FMT  @GEGR1NF           RAW_USER_FORMAT        0#54#1@55#HIGH#2               */
    /* 5786  CLX_SCHEMA  CO1RAW  CO1RAW_AGEGR1__FMT  @GEGR1_F           RAW_USER_FORMAT        1#1#<55 YEARS@2#2#>=55 YEARS   */
    /* 5787  CLX_SCHEMA  CO1RAW  CO1RAW_AVISITN_FMT  @VISITNF           RAW_USER_FORMAT        1#1#3@2#2#6                    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                                _      _
     _ __ ___   ___  __ _ _ __    ___| |_ __| |
    | `_ ` _ \ / _ \/ _` | `_ \  / __| __/ _` |
    | | | | | |  __/ (_| | | | | \__ \ || (_| |
    |_| |_| |_|\___|\__,_|_| |_| |___/\__\__,_|

    */

    proc datasets lib=work nodetails nolist;
      delete xmastatx;
    run;quit;

    %utlnopts;

    %odsoff;

    %do_over(_tblnum _varnum,phrase=%str(

        proc datasets lib=work nodetails nolist;
          delete xma_stat xma_statxpo xma_varadd;
        run;quit;

        proc means data=clxsd1.?_tblnum;
        var ?_varnum;
        output out=xma_stat n=n nmiss=nmiss min=min q1=q1 median= median mean= mean std= std q3= q3 cv= cv max= max;
        run;quit;

        proc transpose data=xma_stat out=xma_statxpo(where=(index(_name_,'_')=0 ));
        run;quit;

        data xma_stats;
          &atr;
          retain Table "?_tblnum" src "CLX_SCHEMA" Variable "?_varnum";
          set xma_statxpo;
          question=cats(substr("?_tblnum",4,3),'_',_name_);
          study=scan("?_tblnum",1,'_');
          answer=cats(col1);
        run;quit;

        proc append data=xma_stats base=xmastatx ;
        run;quit;
    ));

    %odson;
    %utlopts;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* last 41 obs from XMASTATX total obs=1,700 09MAR2022:13:51:26                                                           */
    /*                                                                                                                        */
    /*  Obs       SRC        STUDY       TABLE      VARIABLE    QUESTION      ANSWER                                          */
    /*                                                                                                                        */
    /* 1660    CLX_SCHEMA    C02SDM    C02SDM_LB    LBDY        SDM_MAX       193                                             */
    /* 1661    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_N         177                                             */
    /* 1662    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_NMISS     0                                               */
    /* 1663    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_MIN       1                                               */
    /* 1664    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_Q1        1                                               */
    /* 1665    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_MEDIAN    2                                               */
    /* 1666    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_MEAN      1.988700565                                     */
    /* 1667    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_STD       0.8187344764                                    */
    /* 1668    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_Q3        3                                               */
    /* 1669    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_CV        41.16931884                                     */
    /* 1670    CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_MAX       3                                               */                                                                                   */
    /**************************************************************************************************************************/

    data clx.clx_010mta090;
      &atr;
     do until(fin);
      set clx.clx_010mta080    end=fin;
      output;
     end;
     do until (dne);
      set xmastatx end=dne;
        output;
     end;
     stop;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* Up to 40 obs CLX.CLX_010MTA090 total obs=14,126 09MAR2022:13:52:08                                                     */
    /*                                                                                                                        */
    /* Obs       SRC        STUDY        TABLE       VARIABLE    QUESTION        ANSWER                                       */
    /*                                                                                                                        */
    /*   1    CLX_SCHEMA    C01ADM    C01ADM_ADAE    STUDYID     ADM_LABEL       STUDY IDENTIFIER                             */
    /*   2    CLX_SCHEMA    C01ADM    C01ADM_ADAE    STUDYID     ADM_FORMAT      NULL                                         */
    /*   3    CLX_SCHEMA    C01ADM    C01ADM_ADAE    STUDYID     ADM_INFORMAT    NULL                                         */
    /*   4    CLX_SCHEMA    C01ADM    C01ADM_ADAE    STUDYID     ADM_TYPE        CHAR                                         */
    /*   5    CLX_SCHEMA    C01ADM    C01ADM_ADAE    STUDYID     ADM_LENGTH      15                                           */
    /*   6    CLX_SCHEMA    C01ADM    C01ADM_ADAE    USUBJID     ADM_LABEL       UNIQUE SUBJECT IDENTIFIER                    */
    /*   7    CLX_SCHEMA    C01ADM    C01ADM_ADAE    USUBJID     ADM_FORMAT      NULL                                         */
    /*   8    CLX_SCHEMA    C01ADM    C01ADM_ADAE    USUBJID     ADM_INFORMAT    NULL                                         */
    /*   9    CLX_SCHEMA    C01ADM    C01ADM_ADAE    USUBJID     ADM_TYPE        CHAR                                         */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
         _ _     _   _            _
      __| (_)___| |_(_)_ __   ___| |_
     / _` | / __| __| | `_ \ / __| __|
    | (_| | \__ \ |_| | | | | (__| |_
     \__,_|_|___/\__|_|_| |_|\___|\__|

    */

    %utlnopts;

    proc datasets lib=work nodetails nolist;
      delete xmaunq;
    run;quit;

    %put &=_tbln;

    %do_over(_tbl _var,phrase=%str(

       proc datasets lib=work nodetails nolist;
         delete  xmaunqapn;
       run;quit;

       proc sql;
          create
             table xmaunqapn as
          select
            "CLX_SCHEMA"                              as Src        length=32
            ,scan("?_tbl",1,'_')                      as Study      length=32
            ,"?_tbl"                                  as Table      length=32
            ,"?_var"                                  as Variable   length=32
            ,cats(substr("?_tbl",4,3),"_DISTINCT")    as Question   length=32
            ,cats(count(distinct ?_var))              as answer     length=32
          from
             clxsd1.?_tbl
          group by
             Src
            ,Study
            ,Table
            ,Variable
            ,Question
    ;quit;
    proc append data=xmaunqapn base=xmaunq;
    run;quit;
    ));

    %utlopts;

    data clx.clx_010mta100 ;
      &atr;
     do until(fin);
      set clx.clx_010mta090    end=fin;
      output;
     end;
     do until (dne);
      set xmaunq end=dne;
        src="CLX_SCHEMA";
        output;
     end;
     stop;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* last 41 obs from CLX.CLX_010MTA100 total obs=14,588 09MAR2022:13:53:03                                                 */
    /*                                                                                                                        */
    /*   Obs       SRC        STUDY       TABLE      VARIABLE      QUESTION      ANSWER                                       */
    /*                                                                                                                        */
    /* 14548    CLX_SCHEMA    C02SDM    C02SDM_EX    EXDOSFRM    SDM_DISTINCT     1                                           */
    /* 14549    CLX_SCHEMA    C02SDM    C02SDM_EX    EXDOSTOT    SDM_DISTINCT     3                                           */
    /* 14550    CLX_SCHEMA    C02SDM    C02SDM_EX    EXSTDTC     SDM_DISTINCT     64                                          */
    /* 14551    CLX_SCHEMA    C02SDM    C02SDM_EX    EXENDTC     SDM_DISTINCT     60                                          */
    /* 14552    CLX_SCHEMA    C02SDM    C02SDM_EX    EXSTDY      SDM_DISTINCT     16                                          */
    /* 14553    CLX_SCHEMA    C02SDM    C02SDM_EX    EXENDY      SDM_DISTINCT     38                                          */
    /* 14554    CLX_SCHEMA    C02SDM    C02SDM_LB    STUDYID     SDM_DISTINCT     1                                           */
    /* 14555    CLX_SCHEMA    C02SDM    C02SDM_LB    DOMAIN      SDM_DISTINCT     1                                           */
    /* 14556    CLX_SCHEMA    C02SDM    C02SDM_LB    USUBJID     SDM_DISTINCT     10                                          */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*__                  _                _____  ___
     / _|_ __ ___  __ _  | |_ ___  _ __   |___ / / _ \
    | |_| `__/ _ \/ _` | | __/ _ \| `_ \    |_ \| | | |
    |  _| | |  __/ (_| | | || (_) | |_) |  ___) | |_| |
    |_| |_|  \___|\__, |  \__\___/| .__/  |____/ \___/
                     |_|          |_|
    */

    %utlnopts;

    proc datasets lib=work nodetails nolist;
      delete  xmafrqapn ;
    run;quit;

    %do_over(_tbl _var,phrase=%str(

       proc datasets lib=work nodetails nolist;
         delete xmafrqout xmafix xmarol;
       run;quit;

       ods exclude all;
       ods output onewayfreqs=xmafrqout;
       proc freq data=clxsd1.?_tbl order=freq;
         format ?_var;
         tables ?_var / missing;
       run;quit;
       ods select all;

       data xmafix;
         &atr;
         length ans $200;
         keep ans;
         retain Table "?_tbl" Variable "?_var" ;
         set xmafrqout(obs=30);
         question = cats(substr("?_tbl",4,3),"_FREQUENCIES");
         study=scan("?_tbl",1,'_');
         Variable =  cats('F_',scan( table , 2 , ' '));
         if frequency=. then frequanyy=-1;
         ans=cats(vvaluex(Variable),'=',put(frequency,8. -l));
         Variable=substr(Variable,3);

         select;
           when (ans =: '#') ans=cats('MIS',ans);
           when (ans =: '.') ans=cats('MIS',substr(ans,2));
           otherwise;
         end;
       run;quit;

       data xmarol(rename=tst=answer);
         retain src study table variable question;
         retain tst;
         length tst $8192;
         set xmafix;
         by Variable notsorted;
         tst=catx("@",tst,ans);
         table="?_tbl";
         src = "CLX_SCHEMA" ;
         if last.Variable then output;
         drop answer ans;
       run;quit;

       proc append data=xmarol base=xmafrqapn;
       run;quit;
    ));

    %odson;

    %utlopts;

    data clx.clx_010mta110 ;
      &atr;
     do until(fin);
      set clx.clx_010mta100   end=fin;
      output;
     end;
     do until (dne);
      set xmafrqapn end=dne;
        output;
     end;
     stop;
    run;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* last 41 obs from CLX.CLX_010MTA110 total obs=15,050 09MAR2022:13:54:16                                                 */
    /*                                                                                                                        */
    /*   Obs    SRC        STUDY       TABLE      VARIABLE       QUESTION       ANSWER                                        */
    /*                                                                                                                        */
    /* 15039 CLX_SCHEMA    C02SDM    C02SDM_XP    DOMAIN      SDM_FREQUENCIES   XP=177                                        */
    /* 15041 CLX_SCHEMA    C02SDM    C02SDM_XP    XPSEQ       SDM_FREQUENCIES   1=60@2=59@3=58                                */
    /* 15042 CLX_SCHEMA    C02SDM    C02SDM_XP    XPTESTCD    SDM_FREQUENCIES   XPPAIN=177                                    */
    /* 15043 CLX_SCHEMA    C02SDM    C02SDM_XP    XPTEST      SDM_FREQUENCIES   Pain Score=177                                */
    /* 15044 CLX_SCHEMA    C02SDM    C02SDM_XP    XPORRES     SDM_FREQUENCIES   Severe=58@None=53@Mild=35@Moderate=31         */
    /* 15045 CLX_SCHEMA    C02SDM    C02SDM_XP    VISITNUM    SDM_FREQUENCIES   0=60@1=59@2=58                                */
    /* 15046 CLX_SCHEMA    C02SDM    C02SDM_XP    VISIT       SDM_FREQUENCIES   baseline=60@3 months=59@6 months=58           */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    %utl_dirlst(&root/sas,sas);

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  Up to 40 obs from SAS total obs=18 08MAR2022:11:13:13                                                                 */
    /*                                                                                                                        */
    /*                          NUMBER_OF_    MEMBER_                                                                         */
    /*  Obs      FILENAME         MEMBERS      NUMBER                                                                         */
    /*                                                                                                                        */
    /*    1    c01sdm_Ae.sas        18            1                                                                           */
    /*    2    c01sdm_Dm.sas        18            2                                                                           */
    /*    3    c01sdm_Ex.sas        18            3                                                                           */
    /*    4    c01sdm_Lb.sas        18            4                                                                           */
    /*    5    c01sdm_Xp.sas        18            5                                                                           */
    /*    6    c02sdm_Ae.sas        18            6                                                                           */
    /*    7    c02sdm_Dm.sas        18            7                                                                           */
    /*    8    c02sdm_Ex.sas        18            8                                                                           */
    /*    9    c02sdm_Lb.sas        18            9                                                                           */
    /*   10    c02sdm_Xp.sas        18           10                                                                           */
    /*   11    c02adm_EF.sas        18           11                                                                           */
    /*   12    c02adm_SL.sas        18           12                                                                           */
    /*   13    c02adm_TE.sas        18           13                                                                           */
    /*   14    c02sdm_Ae.sas        18           14                                                                           */
    /*   15    c02sdm_Dm.sas        18           15                                                                           */
    /*   16    c02sdm_Ex.sas        18           16                                                                           */
    /*   17    c02sdm_Lb.sas        18           17                                                                           */
    /*   18    c02sdm_Xp.sas        18           18                                                                                                                     */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    data clx_010mta110sas;
      &atr;
      set sas;
      src="CLX_SCHEMA";
      study  =  upcase(scan(filename,1,'_'));
      table  =  upcase(scan(filename,1,'.'));
      variable =cats('***TABLE_LEVEL***');
      question =cats(substr(upcase(filename),4,3),'_PROGRAM');
      answer=upcase(cats("&root/sas/",filename));
    run;quit;

    data clx.clx_010mta120 ;
      &atr;
     do until(fin);
      set clx.clx_010mta110   end=fin;
      output;
     end;
     do until (dne);
      set clx_010mta110sas end=dne;
        output;
     end;
     stop;
    run;

    %utl_optlenpos(clx.clx_010mta120,clx.clx_010mta120);

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  last 41 obs from CLX.CLX_010MTA120 total obs=15,068 09MAR2022:13:59:17                                                */
    /*                                                                                                                        */
    /*    Obs    SRC        STUDY       TABLE      VARIABLE                QUESTION  ANSWER                                   */
    /*                                                                                                                        */
    /*  15057 CLX_SCHEMA    C01SDM    C01SDM_EX    ***TABLE_LEVEL***    SDM_PROGRAM  D:/CLX/SAS/C01SDM_EX.SAS                 */
    /*  15058 CLX_SCHEMA    C01SDM    C01SDM_LB    ***TABLE_LEVEL***    SDM_PROGRAM  D:/CLX/SAS/C01SDM_LB.SAS                 */
    /*  15059 CLX_SCHEMA    C01SDM    C01SDM_XP    ***TABLE_LEVEL***    SDM_PROGRAM  D:/CLX/SAS/C01SDM_XP.SAS                 */
    /*  15060 CLX_SCHEMA    C02ADM    C02ADM_AE    ***TABLE_LEVEL***    ADM_PROGRAM  D:/CLX/SAS/C02ADM_AE.SAS                 */
    /*  15061 CLX_SCHEMA    C02ADM    C02ADM_EF    ***TABLE_LEVEL***    ADM_PROGRAM  D:/CLX/SAS/C02ADM_EF.SAS                 */
    /*  15062 CLX_SCHEMA    C02ADM    C02ADM_SL    ***TABLE_LEVEL***    ADM_PROGRAM  D:/CLX/SAS/C02ADM_SL.SAS                 */
    /*  15063 CLX_SCHEMA    C02ADM    C02ADM_TE    ***TABLE_LEVEL***    ADM_PROGRAM  D:/CLX/SAS/C02ADM_TE.SAS                 */
    /*  15064 CLX_SCHEMA    C02SDM    C02SDM_AE    ***TABLE_LEVEL***    SDM_PROGRAM  D:/CLX/SAS/C02SDM_AE.SAS                 */
    /*  15065 CLX_SCHEMA    C02SDM    C02SDM_DM    ***TABLE_LEVEL***    SDM_PROGRAM  D:/CLX/SAS/C02SDM_DM.SAS                 */
    /*  15066 CLX_SCHEMA    C02SDM    C02SDM_EX    ***TABLE_LEVEL***    SDM_PROGRAM  D:/CLX/SAS/C02SDM_EX.SAS                 */
    /*  15067 CLX_SCHEMA    C02SDM    C02SDM_LB    ***TABLE_LEVEL***    SDM_PROGRAM  D:/CLX/SAS/C02SDM_LB.SAS                 */
    /*  15068 CLX_SCHEMA    C02SDM    C02SDM_XP    ***TABLE_LEVEL***    SDM_PROGRAM  D:/CLX/SAS/C02SDM_XP.SAS                 */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
                                                 _        _     _
     _ __  _ __ ___   __ _ _ __ __ _ _ __ ___   | |_ __ _| |__ | | ___
    | `_ \| `__/ _ \ / _` | `__/ _` | `_ ` _ \  | __/ _` | `_ \| |/ _ \
    | |_) | | | (_) | (_| | | | (_| | | | | | | | || (_| | |_) | |  __/
    | .__/|_|  \___/ \__, |_|  \__,_|_| |_| |_|  \__\__,_|_.__/|_|\___|
    |_|              |___/
    */

    data clx.clx_010mta130sas;
      length fyl $96 SDTM_PROGRAM   $96 SAS_STATEMENT $384;
      retain LINE_NUMBER 0 SAS_STATEMENT fyl;
      infile "&root/sas/*.sas" filename=fyl;
      input;
      SAS_STATEMENT=_infile_;
      SDTM_PROGRAM=upcase(fyl);
      lagfyl=lag(fyl);
      if lagfyl ne fyl then LINE_NUMBER=0;
      LINE_NUMBER+1;
      if SAS_STATEMENT ne "" then output;
      drop lagfyl;
    run;quit;

    %utl_optlenpos(clx.clx_010mta130sas,clx.clx_010mta130sas);

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* Up to 40 obs CLX.CLX_010MTA120SAS total obs=1,974 08MAR2022:14:23:14                                                   */
    /*                                                                                                                        */
    /*                                                                                                                 LINE_  */
    /*         SDTM_PROGRAM                                                  SAS_STATEMENT                            NUMBER  */
    /*                                                                                                                        */
    /*   D:\CLX\SAS\C01SDM_AE.SAS  %macro cln_525sdtmAe;                                                                1     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS      %cln_140mkeMtyTbl(metadatafile=&gbl_root/xls/cln_sdtmMetadata.xlsx,dataset=AE);  3     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS      %put &AEKEEPSTRING;                                                              5     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS      **** DERIVE THE MAJORITY OF SDTM AE VARIABLES;                                   7     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS      options missing = ' ';                                                           8     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS      data ae;                                                                         9     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS        set EMPTY_AE                                                                  10     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS        cln.cln_rawadverse;                                                           11     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS          studyid = 'XYZ123';                                                         12     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS          domain = 'AE';                                                              13     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS          usubjid = left(uniqueid);                                                   14     */
    /*   D:\CLX\SAS\C01SDM_AE.SAS      run;                                                                            15     */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
