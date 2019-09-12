# utl-hash-filling-in-missing-gender-for-my-patients-appointments
Hash filling in missing gender for my patients appointments 
    SAS Forum: Hash filling in missing gender for my patients appointments                                       
                                                                                                                 
    I have patients scheduled for appointments in January and                                                    
    I need to fill in the missing gender for each appt.                                                          
                                                                                                                 
    This demonstrates an elegant fundamental property of the hash.                                               
                                                                                                                 
    github                                                                                                       
    https://tinyurl.com/y2yyr3le                                                                                 
    https://github.com/rogerjdeangelis/utl-hash-filling-in-missing-gender-for-my-patients-appointments           
                                                                                                                 
    SAS Forum                                                                                                    
    https://tinyurl.com/yyz2rubc                                                                                 
    https://communities.sas.com/t5/SAS-Programming/Fill-empty-spaces-with-the-table-itself/m-p/586635            
                                                                                                                 
    Draycut                                                                                                      
    https://communities.sas.com/t5/user/viewprofilepage/user-id/31304                                            
                                                                                                                 
    *_                   _                                                                                       
    (_)_ __  _ __  _   _| |_                                                                                     
    | | '_ \| '_ \| | | | __|                                                                                    
    | | | | | |_) | |_| | |_                                                                                     
    |_|_| |_| .__/ \__,_|\__|                                                                                    
            |_|                                                                                                  
    ;                                                                                                            
                                                                                                                 
    data have;                                                                                                   
       input appointment$ patient$ gender$ ;                                                                     
    cards4;                                                                                                      
    10JAN19 Joe M                                                                                                
    13JAN19 MAT F                                                                                                
    17JAN19 Joe .                                                                                                
    19JAN19 Tom F                                                                                                
    22JAN19 Moe M                                                                                                
    24JAN19 Moe .                                                                                                
    25JAN19 Ted .                                                                                                
    26JAN19 Ted M                                                                                                
    29JAN19 Tom .                                                                                                
    ;;;;                                                                                                         
    run;quit;                                                                                                    
                                                                                                                 
     WORK.HAVE total obs=9                                                                                       
                                                                                                                 
     APPOINTMENT    PATIENT    GENDER                                                                            
                                                                                                                 
       10JAN19        Joe        M                                                                               
       13JAN19        MAT        F                                                                               
       17JAN19        Joe                                                                                        
       19JAN19        Tom        F                                                                               
       22JAN19        Moe        M                                                                               
       24JAN19        Moe                                                                                        
       25JAN19        Ted                                                                                        
       26JAN19        Ted        M                                                                               
       29JAN19        Tom                                                                                        
                                                                                                                 
    *           _                                                                                                
     _ __ _   _| | ___  ___                                                                                      
    | '__| | | | |/ _ \/ __|                                                                                     
    | |  | |_| | |  __/\__ \                                                                                     
    |_|   \__,_|_|\___||___/                                                                                     
                                                                                                                 
    ;                                                                                                            
                                                                                                                 
    * It is important that the data remains in appointment order.                                                
                                                                                                                 
    * I sort to demonstrate the business rules.                                                                  
                                                                                                                 
    **OUT OF ORDER**                   | RULES                                                                   
    APPOINTMENTS     PATIENT    GENDER | GENDER  (fill in)                                                       
                                       |                                                                         
      10JAN19          Joe        M    |  M                                                                      
      17JAN19          Joe             |  M  ** retain gender                                                    
                                                                                                                 
      13JAN19          MAT        F    |                                                                         
                                                                                                                 
      22JAN19          Moe        M    |  M                                                                      
      24JAN19          Moe             |  M  ** retain gender                                                    
                                                                                                                 
      26JAN19          Ted        M    |  M                                                                      
      25JAN19          Ted             |  M  ** retain gender                                                    
                                                                                                                 
      19JAN19          Tom        F    |  F                                                                      
      29JAN19          Tom             |  F  ** retain gender                                                    
                                                                                                                 
    *            _               _                                                                               
      ___  _   _| |_ _ __  _   _| |_                                                                             
     / _ \| | | | __| '_ \| | | | __|                                                                            
    | (_) | |_| | |_| |_) | |_| | |_                                                                             
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                            
                    |_|                                                                                          
    ;                                                                                                            
                                                                                                                 
    WORK.WANT total obs=9                                                                                        
                                                                                                                 
     APPOINTMENT    PATIENT    GENDER                                                                            
                                                                                                                 
       10JAN19        Joe        M                                                                               
       13JAN19        MAT        F                                                                               
       17JAN19        Joe        M    ** matches 10JAN19                                                         
       19JAN19        Tom        F                                                                               
       22JAN19        Moe        M                                                                               
       24JAN19        Moe        M                                                                               
       25JAN19        Ted        M                                                                               
       26JAN19        Ted        M                                                                               
       29JAN19        Tom        F                                                                               
                                                                                                                 
    *                                                                                                            
     _ __  _ __ ___   ___ ___  ___ ___                                                                           
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|                                                                          
    | |_) | | | (_) | (_|  __/\__ \__ \                                                                          
    | .__/|_|  \___/ \___\___||___/___/                                                                          
    |_|                                                                                                          
    ;                                                                                                            
                                                                                                                 
    data want(drop=rc);                                                                                          
                                                                                                                 
        if _N_=1 then do;                                                                                        
            declare hash h(dataset:"have(where=(not missing(gender))");                                          
            h.definekey('patient');                                                                              
            h.definedata('gender');                                                                              
            h.definedone();                                                                                      
        end;                                                                                                     
                                                                                                                 
        set have;                                                                                                
                                                                                                                 
        if missing(gender) then rc=h.find();                                                                     
    run;                                                                                                         
                                                                                                                 
