# utl_keep_only_the_columns_that_exist_in_another_table
Keep only the columns that exist in another table.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Keep only the columns that exist in another table

    github
    https://tinyurl.com/ybrd323o
    https://github.com/rogerjdeangelis/utl_keep_only_the_columns_that_exist_in_another_table

    On sunday I get a template dataset which has the variables
    I  need to keep for the rest of the week.
    The name and type of extra variables on susequent days is not known.
    
    For Additional solutions see
    Quentin McMullen
    Keintz, Mark
    on the end of this message


    Inspired by
    https://tinyurl.com/y99e9sug
    https://communities.sas.com/t5/Base-SAS-Programming/How-to-drop-or-keep-variables-conditionally/m-p/474635

    Love the questions thta come from SAS Communties.
    Op is not stuck in box that I am stuck in.


    INPUT
    =====

    On Sunday I get the variables for the entire week;

     WORK.SUNDAY total obs=19

       DAY      NAME       AGE

      Sunday    Alfred      14
      Sunday    Alice       13
      Sunday    Barbara     13
      Sunday    Carol       14
      Sunday    Henry       14
      Sunday    James       12
     ...

    Typical Monday dataset
    Need to drop extra1 and extra2
                                       RULE
                                       ====
     WORK.MONDAY total obs=19         DROP THESE
                                   -----------------
       DAY      NAME       AGE     EXTRA1    EXTRA2

      Monday    Alfred      14       M         196
      Monday    Alice       13       F         169
      Monday    Barbara     13       F         169
      Monday    Carol       14       F         196
      Monday    Henry       14       M         196
      Monday    James       12       M         144
    ...


    PROCESS
    =======

    data want;
      retain day;
      set monday(where=(0=
                   %sysfunc(dosubl('
                   proc sql; select name into :names separated by " "
                   from sashelp.vcolumn where libname="WORK" and memname="SUNDAY";quit;
               '))));
      keep &names;

    run;quit;


    OUTPUT
    ======

     WORK.WANT total obs=19

       DAY      NAME       AGE

      Monday    Alfred      14
      Monday    Alice       13
      Monday    Barbara     13
      Monday    Carol       14
      Monday    Henry       14
    ...

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

    data sunday;
      retain day;
      set sashelp.class(keep=name age);
      day="Sunday";
    run;quit;

    data monday;
      retain day;
      set sashelp.class(keep=name age sex age);
      day="Monday";
      extra1=sex;
      extra2=age*age;
      drop sex;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;
    see process


    *
     _ __ ___   ___  _ __ ___  __      ____ _ _   _ ___
    | '_ ` _ \ / _ \| '__/ _ \ \ \ /\ / / _` | | | / __|
    | | | | | | (_) | | |  __/  \ V  V / (_| | |_| \__ \
    |_| |_| |_|\___/|_|  \___|   \_/\_/ \__,_|\__, |___/
                                              |___/
    ;

    Thanks Roger,

    I share your enthusiasm for DOSUBL.  For this use case, I like the idea of
    a %VARLIST function-style macro which uses DOSUBL behind the scenes, like John
    King's %Expand_Varlist https://www.mwsug.org/proceedings/2017/BB/MWSUG-2017-BB142.pdf.
    You can't get much more readable than:

    data want ;
      set Monday(keep=%expand_varlist(data=Sunday)) ;
    run ;

    Another common approach is to create a 0 record shell data set, and then append:

    data want ;
      set Sunday (obs=0) ;
    run ;

    proc append base=want data=Monday force nowarn ;
    run ;

    Kind Regards,
    --Q.




    Keintz, Mark
    12:46 PM (48 minutes ago)
    to me, SAS-L
    This is why there are well-known rules for PDV construction in the data step:

    data sunday;
      set sashelp.class (keep=name age sex);
      where sex='F';
    run;

    data monday;
      set sashelp.class;
      where sex='M';
    run;

    data want(drop=_sentinel: );
      retain _sentinel1 .;
      if 0 then set sunday;
      retain _sentinel2 . ;
      set monday;
      keep _sentinel1 -- _sentinel2;
    run;


    Roger DeAngelis <rogerjdeangelis@gmail.com>
    1:31 PM (3 minutes ago)
    to Mark, SAS-L
    Thanks Q and Mark

    I think I have DOSUBitis, perhaps like Paul has Hashitis?

    Nice additions will archive

    Mark

      Nice (out of the box)

      Placing a variable before and after the variables in Sunday and then
      keeping everything between the sentinels and dropping the sentinels on output.











