# utl-creating-a-sas-table-from-a-json-file-using-r-jsonlite-and-sas
Creating a sas table from a json file using r jsonlite and sas
    %let pgm=utl-creating-a-sas-table-from-a-json-file-using-r-jsonlite-and-sas;

    %stop_submission;

    Creating a sas table from a json file using r jsonlite and sas

      CONTENTS

           1 r jsonlite
           2 sas libname engine
           3 related repos

    github
    https://tinyurl.com/y9sf8aws
    https://github.com/rogerjdeangelis/utl-creating-a-sas-table-from-a-json-file-using-r-jsonlite-and-sas

    communities.sas
    https://tinyurl.com/2h2yxduv
    https://communities.sas.com/t5/SAS-Programming/Maybe-I-do-not-have-the-JSON-Engine/m-p/810902#M319796

    SOAPBOX ON

     TWO COMMENTS

       1 List structure and JSON file stucture
         The R list strucure can capture the structure of
         the JSON file and is very useful when parsing
         complex JSOn files

       2 JSON meta data
         It is preferable to create a separate dataset
         sas dataset,
         separate

    1 R list structure

      have List of 3 (people dataframe and two meta data variables)

       $ people :'data.frame':12 obs. of  2...
        ..$ craft: chr [1:12] "ISS" "ISS" "I...
        ..$ name : chr [1:12] "Oleg Kononenk...

       $ number : int 12                    ...
       $ message: chr "success"             ...

    2  Meta data

       R does not capture the last line in the jso file
       as part of the people dataframe.

       "number":12, "message": "success" are
       meta data where number is the nuber of rows in the people dataframe.
        rray.
       SAS places the meta data with the people dataset.

       Instead I capture the meta data in a separate dataframe.

       These keys 'number' and 'message' are part of the JSON meta data.
       They are top-level properties of the JSON object.
       In JSON, all key-value pairs inside the curly braces {}
       are part of the data structure, regardless of their names.

       "number": 12 is a property with a numeric value.
       "message": "success" is a property with a string value.

       These are not variables in the programming sense,
       but rather properties (also called keys or fields) in the JSON objec
    t. They are included in the data and will be accessible
       when the JSON is parsed in any standard-compliant parser.


    SOAPBOX OFF

    /**************************************************************************************************************************/
    /*               INPUT                           |              PROCESS            |     OUTPUT                           */
    /*               ======                          |              =======            |     ======                           */
    /* *---- I downloaded the json file from         | 1 R JSONLITE                    | d:/txt/meta.txt (json structure)     */
    /* http://api.open-notify.org/astros.json        | ============                    |                                      */
    /* ----*;                                        |                                 | have List of 3                       */
    /*                                               | %utl_rbeginx;                   | $ people :'data.frame':12 obs.       */
    /* d:/json/have.json                             | parmcards4;                     |  .$ craft:chr[1:12] "ISS" "ISS" "I.. */
    /*                                               | library(jsonlite)               |  .$ name :chr[1:12] "Oleg Kononenk.. */
    /* {"people": [                                  | library(data.table)             | $ number :int 12                  .. */
    /* {"craft":"ISS","name":"Oleg Kononenko"},      | library(tidyverse)              | $ message:chr "success"           .. */
    /* {"craft":"ISS","name":"Nikolai Chub"},        | source("c:/oto/fn_tosas9x.R")   |                                      */
    /* {"craft":"ISS","name":"Tracy Caldwell Dyson"},| have<-                          | SD1.META total obs=1                 */
    /* {"craft":"ISS","name":"Matthew Dominick"},    |   fromJSON("d:/json/have.json") |                                      */
    /* {"craft":"ISS","name":"Michael Barratt"},     | str(have)                       |  NUMBER    MESSAGE                   */
    /* {"craft":"ISS","name":"Jeanette Epps"},       | sink("d:/txt/meta.txt")         |                                      */
    /* {"craft":"ISS","name":"Alexander Grebenkin"}, | str(have)                       |    12      success                   */
    /* {"craft":"ISS","name":"Butch Wilmore"},       | sink()                          |                                      */
    /* {"craft":"ISS","name":"Sunita Williams"},     | want<-as.data.frame(have[1])    | SD1.WANT total obs=12                */
    /* {"craft":"Tiangong", "name": "Li Guangsu"},   | meta<-cbind(                    |                                      */
    /* {"craft":"Tiangong", "name": "Li Cong"},      |   as.data.frame(have[2])        | PEOPLE_                              */
    /* {"craft":"Tiangong", "name": "Ye Guangfu"}],  |  ,as.data.frame(have[3]))       | CRAFT       PEOPLE_NAME              */
    /* "number":12, "message": "success"}            | meta                            |                                      */
    /*                                               | want                            | ISS         Oleg Kononenko           */
    /*                                               | fn_tosas9x(                     | ISS         Nikolai Chub             */
    /* *---- save file ----*;                        |       inp    = want             | ISS         Tracy Caldwell Dyson     */
    /* filename ft15f001 "d:/json/have.json";        |      ,outlib ="d:/sd1/"         | ISS         Matthew Dominick         */
    /* parmcards4;                                   |      ,outdsn ="want"            | ISS         Michael Barratt          */
    /* {"people": [                                  |      )                          | ISS         Jeanette Epps            */
    /* {"craft":"ISS","name":"Oleg Kononenko"},      | fn_tosas9x(                     | ISS         Alexander Grebenkin      */
    /* {"craft":"ISS","name":"Nikolai Chub"},        |       inp    = meta             | ISS         Butch Wilmore            */
    /* {"craft":"ISS","name":"Tracy Caldwell Dyson"},|      ,outlib ="d:/sd1/"         | ISS         Sunita Williams          */
    /* {"craft":"ISS","name":"Matthew Dominick"},    |      ,outdsn ="meta"            | Tiangong    Li Guangsu               */
    /* {"craft":"ISS","name":"Michael Barratt"},     |      )                          | Tiangong    Li Cong                  */
    /* {"craft":"ISS","name":"Jeanette Epps"},       | ;;;;                            | Tiangong    Ye Guangfu               */
    /* {"craft":"ISS","name":"Alexander Grebenkin"}, | %utl_rendx;                     |                                      */
    /* {"craft":"ISS","name":"Butch Wilmore"},       |                                 |                                      */
    /* {"craft":"ISS","name":"Sunita Williams"},     | data _null_ ;                   |                                      */
    /* {"craft":"Tiangong", "name": "Li Guangsu"},   |  infile "d:/txt/meta.txt";      |                                      */
    /* {"craft":"Tiangong", "name": "Li Cong"},      |  input;                         |                                      */
    /* {"craft":"Tiangong", "name": "Ye Guangfu"}],  |  put _infile_ $38. "...";       |                                      */
    /* "number":12, "message": "success"}            |                                 |                                      */
    /* ;;;;                                          | run;quit;                       |                                      */
    /* run;quit;                                     |                                 |                                      */
    /*                                               |------------------------------------------------------------------------*/
    /*                                               | 2 SAS LIBNAME ENGINE            |WORK.WANT total obs=38                */
    /*                                               | ====================            |                                      */
    /*                                               |                                 | P   P1     P2   V VALUE              */
    /*                                               | libname jsn json                |                                      */
    /*                                               |   "d:/json/have.json"           | 1 people       0                     */
    /*                                               |    automap=reuse;               | 2 people craft 1 ISS                 */
    /*                                               | data want;                      | 2 people name  1 Oleg Kononenko      */
    /*                                               |   set jsn.alldata;              | 1 people       0                     */
    /*                                               | run;quit;                       | 2 people craft 1 ISS                 */
    /*                                               |                                 | 2 people name  1 Nikolai Chub        */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 ISS                 */
    /*                                               |                                 | 2 people name  1 Tracy Caldwell Dyson*/
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 ISS                 */
    /*                                               |                                 | 2 people name  1 Matthew Dominick    */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 ISS                 */
    /*                                               |                                 | 2 people name  1 Michael Barratt     */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 ISS                 */
    /*                                               |                                 | 2 people name  1 Jeanette Epps       */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 ISS                 */
    /*                                               |                                 | 2 people name  1 Alexander Grebenkin */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 ISS                 */
    /*                                               |                                 | 2 people name  1 Butch Wilmore       */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 ISS                 */
    /*                                               |                                 | 2 people name  1 Sunita Williams     */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 Tiangong            */
    /*                                               |                                 | 2 people name  1 Li Guangsu          */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 Tiangong            */
    /*                                               |                                 | 2 people name  1 Li Cong             */
    /*                                               |                                 | 1 people       0                     */
    /*                                               |                                 | 2 people craft 1 Tiangong            */
    /*                                               |                                 | 2 people name  1 Ye Guangfu          */
    /*                                               |                                 | 1 number       1 12                  */
    /*                                               |                                 |                                      */
    /*                                               |                                 | 1 message      1 success *meta data* */
    /**************************************************************************************************************************/*/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    %utlfkil(d:/json/have.json);

    filename ft15f001 "d:/json/have.json";
    parmcards4;
    {"people": [
    {"craft":"ISS","name":"Oleg Kononenko"},
    {"craft":"ISS","name":"Nikolai Chub"},
    {"craft":"ISS","name":"Tracy Caldwell Dyson"},
    {"craft":"ISS","name":"Matthew Dominick"},
    {"craft":"ISS","name":"Michael Barratt"},
    {"craft":"ISS","name":"Jeanette Epps"},
    {"craft":"ISS","name":"Alexander Grebenkin"},
    {"craft":"ISS","name":"Butch Wilmore"},
    {"craft":"ISS","name":"Sunita Williams"},
    {"craft":"Tiangong", "name": "Li Guangsu"},
    {"craft":"Tiangong", "name": "Li Cong"},
    {"craft":"Tiangong", "name": "Ye Guangfu"}],
    "number":12, "message": "success"}
    ;;;;
    run;quit;

    /*            _                 _ _ _
    / |  _ __    (_)___  ___  _ __ | (_) |_ ___
    | | | `__|   | / __|/ _ \| `_ \| | | __/ _ \
    | | | |      | \__ \ (_) | | | | | | ||  __/
    |_| |_|     _/ |___/\___/|_| |_|_|_|\__\___|
               |__/
    */

    proc datasets lib=sd1 nolist nodetails;
     delete want meta;
    run;quit;

    %utlfkil(d:/txt/meta.txt);

    %utl_rbeginx;
    parmcards4;
    library(jsonlite)
    library(data.table)
    library(tidyverse)
    source("c:/oto/fn_tosas9x.R")
    have<-
      fromJSON("d:/json/have.json")
    str(have)
    sink("d:/txt/meta.txt")
    str(have)
    sink()
    want<-as.data.frame(have[1])
    meta<-cbind(
      as.data.frame(have[2])
     ,as.data.frame(have[3]))
    meta
    want
    fn_tosas9x(
          inp    = want
         ,outlib ="d:/sd1/"
         ,outdsn ="want"
         )
    fn_tosas9x(
          inp    = meta
         ,outlib ="d:/sd1/"
         ,outdsn ="meta"
         )
    ;;;;
    %utl_rendx;

    data _null_ ;
     infile "d:/txt/meta.txt";
     input;
     put _infile_ $38. "...";

    run;quit;

    proc print data=sd1.meta;
    run;quit;

    proc print data=sd1.want;
    run;quit;

    /**************************************************************************************************************************/
    /*  d:/txt/meta.txt (json structure)     |  SD1.META total obs=1  |   SD1.WANT total obs=12                               */
    /*                                       |                        |                                                       */
    /*  have List of 3                       |   NUMBER    MESSAGE    |   PEOPLE_                                             */
    /*  $ people :'data.frame':12 obs.       |                        |   CRAFT       PEOPLE_NAME                             */
    /*   .$ craft:chr[1:12] "ISS" "ISS" "I.. |     12      success    |                                                       */
    /*   .$ name :chr[1:12] "Oleg Kononenk.. |                        |   ISS         Oleg Kononenko                          */
    /*  $ number :int 12                  .. |                        |   ISS         Nikolai Chub                            */
    /*  $ message:chr "success"           .. |                        |   ISS         Tracy Caldwell Dyson                    */
    /*                                       |                        |   ISS         Matthew Dominick                        */
    /*                                       |                        |   ISS         Michael Barratt                         */
    /*                                       |                        |   ISS         Jeanette Epps                           */
    /*                                       |                        |   ISS         Alexander Grebenkin                     */
    /*                                       |                        |   ISS         Butch Wilmore                           */
    /*                                       |                        |   ISS         Sunita Williams                         */
    /*                                       |                        |   Tiangong    Li Guangsu                              */
    /*                                       |                        |   Tiangong    Li Cong                                 */
    /*                                       |                        |   Tiangong    Ye Guangfu                              */
    /**************************************************************************************************************************/

    /*___                    _ _ _                                   _
    |___ \   ___  __ _ ___  | (_) |__  _ __   __ _ _ __ ___   ___   (_)___  ___  _ __
      __) | / __|/ _` / __| | | | `_ \| `_ \ / _` | `_ ` _ \ / _ \  | / __|/ _ \| `_ \
     / __/  \__ \ (_| \__ \ | | | |_) | | | | (_| | | | | | |  __/  | \__ \ (_) | | | |
    |_____| |___/\__,_|___/ |_|_|_.__/|_| |_|\__,_|_| |_| |_|\___| _/ |___/\___/|_| |_|
                                                                  |__/
    */

    proc datasets lib=sd1 nolist nodetails;
     delete want;
    run;quit;

    libname jsn json
      "d:/json/have.json"
       automap=reuse;

    data want;
      set jsn.alldata;
    run;quit;


    /**************************************************************************************************************************/
    /* WORK.WANT total obs=38                                                                                                 */
    /* Obs    P      P1        P2      V    VALUE                                                                             */
    /*                                                                                                                        */
    /*   1    1    people              0                                                                                      */
    /*   2    2    people     craft    1    ISS                                                                               */
    /*   3    2    people     name     1    Oleg Kononenko                                                                    */
    /*   4    1    people              0                                                                                      */
    /*   5    2    people     craft    1    ISS                                                                               */
    /*   6    2    people     name     1    Nikolai Chub                                                                      */
    /*   7    1    people              0                                                                                      */
    /*   8    2    people     craft    1    ISS                                                                               */
    /*   9    2    people     name     1    Tracy Caldwell Dyson                                                              */
    /*  10    1    people              0                                                                                      */
    /*  11    2    people     craft    1    ISS                                                                               */
    /*  12    2    people     name     1    Matthew Dominick                                                                  */
    /*  13    1    people              0                                                                                      */
    /*  14    2    people     craft    1    ISS                                                                               */
    /*  15    2    people     name     1    Michael Barratt                                                                   */
    /*  16    1    people              0                                                                                      */
    /*  17    2    people     craft    1    ISS                                                                               */
    /*  18    2    people     name     1    Jeanette Epps                                                                     */
    /*  19    1    people              0                                                                                      */
    /*  20    2    people     craft    1    ISS                                                                               */
    /*  21    2    people     name     1    Alexander Grebenkin                                                               */
    /*  22    1    people              0                                                                                      */
    /*  23    2    people     craft    1    ISS                                                                               */
    /*  24    2    people     name     1    Butch Wilmore                                                                     */
    /*  25    1    people              0                                                                                      */
    /*  26    2    people     craft    1    ISS                                                                               */
    /*  27    2    people     name     1    Sunita Williams                                                                   */
    /*  28    1    people              0                                                                                      */
    /*  29    2    people     craft    1    Tiangong                                                                          */
    /*  30    2    people     name     1    Li Guangsu                                                                        */
    /*  31    1    people              0                                                                                      */
    /*  32    2    people     craft    1    Tiangong                                                                          */
    /*  33    2    people     name     1    Li Cong                                                                           */
    /*  34    1    people              0                                                                                      */
    /*  35    2    people     craft    1    Tiangong                                                                          */
    /*  36    2    people     name     1    Ye Guangfu                                                                        */
    /*  37    1    number              1    12                                                                                */
    /*  38    1    message             1    success                                                                           */
    /**************************************************************************************************************************/

    /*____            _       _           _
    |___ /   _ __ ___| | __ _| |_ ___  __| |  _ __ ___ _ __   ___  ___
      |_ \  | `__/ _ \ |/ _` | __/ _ \/ _` | | `__/ _ \ `_ \ / _ \/ __|
     ___) | | | |  __/ | (_| | ||  __/ (_| | | | |  __/ |_) | (_) \__ \
    |____/  |_|  \___|_|\__,_|\__\___|\__,_| |_|  \___| .__/ \___/|___/
                                                      |_|
    */

    REPO
    -------------------------------------------------------------------------------------------------------------------------------------------
    https://github.com/rogerjdeangelis/utl-accessing-specific-value-from-JSON-file
    https://github.com/rogerjdeangelis/utl-converting-a-simple-nested-json-file-to-a-sas-dataset
    https://github.com/rogerjdeangelis/utl-converting-a-two-deep-nested-json-file-to-retangular-sas-tables
    https://github.com/rogerjdeangelis/utl-create-a-specific-json-list-structure-from-R-lists
    https://github.com/rogerjdeangelis/utl-creating-a-sas-table-from-a-json-file-using-r-and-sas
    https://github.com/rogerjdeangelis/utl-export-jsonlines-newline-delimited-json-records-streamout
    https://github.com/rogerjdeangelis/utl-extracting-data-from-a-json-file-from-api-using-r-lists
    https://github.com/rogerjdeangelis/utl-import-a-json-file-with-three-deep-nesting
    https://github.com/rogerjdeangelis/utl-import-dbf-dif-ods-xlsx-spss-json-stata-csv-html-xml-tsv-files-without-sas-access-products
    https://github.com/rogerjdeangelis/utl-import-json-file-and-export-to-sas-with-long-variable-names
    https://github.com/rogerjdeangelis/utl-importing-a-four-deep-nested-JSON-file-into-SAS-using-R
    https://github.com/rogerjdeangelis/utl-importing-simple-json-a-comparison-of-R-and-SAS
    https://github.com/rogerjdeangelis/utl-parsing-a-simple-json-file-in-r-and-sas
    https://github.com/rogerjdeangelis/utl-parsing-json-strings-longer-than_32k-bytes-R-and-SAS
    https://github.com/rogerjdeangelis/utl-proper-handling-of-nulls-when-creating-json-output-to-load-into-databases
    https://github.com/rogerjdeangelis/utl-simple-json-file-to-sas-table-using-sas-and-r
    https://github.com/rogerjdeangelis/utl-very-simple-json-file-to-sas-table-using-r-and-sas
    https://github.com/rogerjdeangelis/utl_SAS_dataset_to_json_using_SAS_R_Python_and_WPS
    https://github.com/rogerjdeangelis/utl_converting_simple_json_file_to_a_sas_table
    https://github.com/rogerjdeangelis/utl_converting_simple_json_file_to_a_sas_table2
    https://github.com/rogerjdeangelis/utl_converting_very_simple_json_file_to_a_sas_dataset
    https://github.com/rogerjdeangelis/utl_create_database_table_from_complex_json_file
    https://github.com/rogerjdeangelis/utl_creating_sas_wps_datasets_from_complex_ill_formed_json_files
    https://github.com/rogerjdeangelis/utl_how_to_stream_stacked_multiple_json_files_into_sas_dataset
    https://github.com/rogerjdeangelis/utl_import_json_r_2_lines_of_code
    https://github.com/rogerjdeangelis/utl_importing_json_file_data_into_sas_as_a_dataset
    https://github.com/rogerjdeangelis/utl_parsing_a_complex_nested_json_file_using_r_lists

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
