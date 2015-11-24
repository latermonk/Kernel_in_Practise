

<font color="#4590a3" size = "6px">start_kernel()->rest_init()->kernel_init-></font>



if (  
    	!try_to_run_init_process("/sbin/init") ||  
	    !try_to_run_init_process("/etc/init") ||  
	    !try_to_run_init_process("/bin/init") ||  
	    !try_to_run_init_process("/bin/sh")

   )


  
return 0;


