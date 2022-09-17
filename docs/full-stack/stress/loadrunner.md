# loadrunner


### test.h
```c
#include "test.h"

Action()
{
	
	int HttpRetCode;
	                                     
	lr_start_transaction("test_api");

	web_custom_request("test_api", 
        "URL=https://www.baidu.com",
		"Method=GET",
		LAST);


        HttpRetCode = web_get_int_property(HTTP_INFO_RETURN_CODE);

	if(HttpRetCode == 200){
        lr_end_transaction("test_api", LR_PASS);
	}
		
	return 0;
}
```