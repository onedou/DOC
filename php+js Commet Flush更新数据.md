#php+js Commet Flush更新数据

###php

	public function IMgetMsg(){
	    header("Cache-Control: no-cache, must-revalidate");
	    header("Access-Control-Allow-Origin: http://appbaba.jios.org:8081");
	    vendor('IMcomet.IMcomet');
	
	    $comet = new IMComet();
	
	    $i = 0;
	
	    $callback = function(){
	        global $i;
	        $i++;
	        $a = '{"abc":"'.$i.'"}';
	        return $a;
	    };
	    
	    $comet->getIMmsg($callback);
	}

###js

	function connect(){
	    var xhr = new XMLHttpRequest();
	    var lastResponseText = '';
	    var activeResponseText = '';
	
	    xhr.open('GET',url);
	    var i = 1;
	    xhr.onreadystatechange = function(){
	        activeResponseText = xhr.responseText;
	        // console.log(activeResponseText+'\n');
	        // console.log(lastResponseText+'\n');
	        activeResponseText = activeResponseText.replace(lastResponseText,'');
	        activeResponseText = activeResponseText.replace(/\s+/g,"");
	        lastResponseText = xhr.responseText;
	
	        console.log(activeResponseText.length);
	
	        if(activeResponseText != ''){
	            var data = $.parseJSON(activeResponseText);
	            console.log(data);
	        }
	
	        if(xhr.readyState == 4 && xhr.status == 200){
	            setTimeout(function(){
	                connect();
	            },5000);
	        }
	    };
	
	    xhr.send(url);
	}

