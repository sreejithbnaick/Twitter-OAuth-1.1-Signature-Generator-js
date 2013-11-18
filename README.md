OAuth 1.1 Header/Signature Generator, js library for Parse Cloud code
=================================================================

Javascript library for Oauth 1.1 Header & signature generation (Twitter mainly),.

Orginal Source code: https://code.google.com/p/oauth (http://oauth.googlecode.com/)

How To Do
=========

1). Upload oauth.js,sha1.js into Parse cloud

2). Add the following code at the top of your main.js

				var oauth = require('cloud/oauth.js');
				var sha = require('cloud/sha1.js');
				
Sample Code
===========
For Twitter posting.

	Parse.Cloud.define("ShareToTwitter", function(request, response){
    	var urlLink = 'https://api.twitter.com/1.1/statuses/update.json';
    	
    	var postSummary="<Add-post-summary>";
  		var status = oauth.percentEncode(postSummary);
  		var consumerSecret = "<add-the-consumer-secret-of-twitter-application>";
  		var tokenSecret = "<add-the-access-token-secret-of-user>";
  		var oauth_consumer_key = "<add-the-consumer-key-of-twitter-app>";
  		var oauth_token = "<add-the-auth-token-of-user>";
  		
  	    var nonce = oauth.nonce(32);
        var ts = Math.floor(new Date().getTime() / 1000);
        var timestamp = ts.toString();
        
        var accessor = {"consumerSecret" : consumerSecret, "tokenSecret":tokenSecret};
        var message = {"method" : "POST", "action": urlLink, "parameters":oauth.decodeForm("status="+status)};
        message.parameters.push(["oauth_version","1.0"]);
        message.parameters.push(["oauth_consumer_key", oauth_consumer_key]);
        message.parameters.push(["oauth_token", oauth_token]);
        message.parameters.push(["oauth_timestamp",timestamp]);
        message.parameters.push(["oauth_nonce", nonce]);
        message.parameters.push(["oauth_signature_method", "HMAC-SHA1"]);
        
        //lets create signature
        oauth.SignatureMethod.sign(message, accessor);
        var normPar = oauth.SignatureMethod.normalizeParameters(message.parameters);
        console.log("Normalized Parameters: "+normPar);
        var baseString = oauth.SignatureMethod.getBaseString(message);
        console.log("BaseString: "+baseString);
        var sig = oauth.getParameter(message.parameters, "oauth_signature")+"=";
        console.log("Non-Encode Signature: "+sig);
        var encodedSig=oauth.percentEncode(sig); //finally you got oauth signature
        console.log("Encoded Signature: "+encodedSig);
    
     Parse.Cloud.httpRequest({
        method: 'POST',
        url: urlLink,
        headers: {
	        "Authorization": 'OAuth oauth_consumer_key="xxxxxxxxxxxxxxxxx", oauth_nonce='+nonce+', oauth_signature='+encodedSig+', oauth_signature_method="HMAC-SHA1", oauth_timestamp='+timestamp+',oauth_token="xxxxxxxxxxxxxxxxxxxxxxx", oauth_version="1.0"'
       },
       body: {
      	 "status" : postSummary,      
       },
       success: function(httpResponse) {
      	 response.success(httpResponse.text);
       },
       error: function(httpResponse) {
        	response.error('Request failed with response' + httpResponse.status);
       }
     });
    });
