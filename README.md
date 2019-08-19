# utl-using-dosubl-and-a-dynamic-arrays-to-add-new-variables
Using dosubl and a dynamic arrays to add new variables
    Using dosubl and a dynamic array to add new variables                                                       
                                                                                                                
    Problem: Op want to add a variable using the following code (without transposing)                           
                                                                                                                
      Data test;                                                                                                
         call symputx('var','age');                                                                             
         &var=33;                                                                                               
      run;quit;                                                                                                 
                                                                                                                
      WARNING: Apparent symbolic reference VAR not resolved.                                                    
                                                                                                                
      Op wanted                                                                                                 
                                                                                                                
      WORK.WANT total obs=2                                                                                     
                                                                                                                
         AGE                                                                                                    
         33                                                                                                     
                                                                                                                
    github                                                                                                      
    https://tinyurl.com/y4spb53d                                                                                
    https://github.com/rogerjdeangelis/utl-using-dosubl-and-a-dynamic-arrays-to-add-new-variables               
                                                                                                                
    StackOverflow                                                                                               
    https://tinyurl.com/yy4b3kxz                                                                                
    https://stackoverflow.com/questions/57437294/symput-and-scoping-in-data-step                                
                                                                                                                
    *_                   _                                                                                      
    (_)_ __  _ __  _   _| |_                                                                                    
    | | '_ \| '_ \| | | | __|                                                                                   
    | | | | | |_) | |_| | |_                                                                                    
    |_|_| |_| .__/ \__,_|\__|                                                                                   
            |_|                                                                                                 
    ;                                                                                                           
                                                                                                                
                                                                                                                
    data have;                                                                                                  
     infile datalines;                                                                                          
     input Month $ Actual;                                                                                      
    cards4;                                                                                                     
    June 4                                                                                                      
    June 5                                                                                                      
    June 6                                                                                                      
    May 1                                                                                                       
    May 2                                                                                                       
    May 3                                                                                                       
    ;;;;                                                                                                        
    run;                                                                                                        
                                                                                                                
                                                                                                                
    /*                                                                                                          
    WORK.HAVE total obs=6                                                                                       
                                                                                                                
      MONTH    ACTUAL                                                                                           
                                                                                                                
      June        4                                                                                             
      June        5                                                                                             
      June        6                                                                                             
      May         1                                                                                             
      May         2                                                                                             
      May         3                                                                                             
    */                                                                                                          
                                                                                                                
    *            _               _                                                                              
      ___  _   _| |_ _ __  _   _| |_                                                                            
     / _ \| | | | __| '_ \| | | | __|                                                                           
    | (_) | |_| | |_| |_) | |_| | |_                                                                            
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                           
                    |_|                                                                                         
    ;                                                                                                           
                                                                                                                
                                                                                                                
    Up to 40 obs WORK.WANT total obs=2                RULES                                                     
                                                      =====                                                     
    Obs    JUNE    MAY    MONTH    ACTUAL    SUM   |  Add variables JUNE and MAY                                
                                                   |  with the sum of actual                                    
     1      15      .     June        6       15   |                                                            
     2       .      6     May         3        6   |                                                            
                                                                                                                
    *          _       _   _                                                                                    
     ___  ___ | |_   _| |_(_) ___  _ __                                                                         
    / __|/ _ \| | | | | __| |/ _ \| '_ \                                                                        
    \__ \ (_) | | |_| | |_| | (_) | | | |                                                                       
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|                                                                       
                                                                                                                
    ;                                                                                                           
                                                                                                                
    %symdel months / nowarn;;                                                                                   
    data want;                                                                                                  
                                                                                                                
      if _n_=0 then do; %let rc=%sysfunc(dosubl('                                                               
          proc sql;                                                                                             
             select distinct month into: months separated by " " from have                                      
          ;quit;                                                                                                
          '));                                                                                                  
          array mons[&sqlobs] &months;                                                                          
      end;                                                                                                      
                                                                                                                
      set have;                                                                                                 
      by  month ;                                                                                               
                                                                                                                
      retain idx sum 0;                                                                                         
                                                                                                                
      if first.month then sum = 0;                                                                              
                                                                                                                
      sum = sum + actual;                                                                                       
                                                                                                                
      if last.month then do;                                                                                    
        idx=idx+1;                                                                                              
        mons[idx] = sum;                                                                                        
        output;                                                                                                 
                                                                                                                
      end;                                                                                                      
                                                                                                                
      drop idx;                                                                                                 
                                                                                                                
    run;quit;                                                                                                   
                                                                                                                
