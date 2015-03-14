OAuth 1.1 Header/Signature Generator, js library for Parse Cloud code (Not Maintaining)
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

	Parse.Cloud.define("Twitter", function(request, response) {
		var urlLink = 'https://api.twitter.com/1.1/statuses/update.json';
	
		var postSummary = request.params.status;
		var status = oauth.percentEncode(postSummary);
		var consumerSecret = "<your_consumer_secret>";
		var tokenSecret = "<your_secret_token_here>";
		var oauth_consumer_key = "<your_oauth_consumer_key>";
		var oauth_token = "<your_oauth_token_here>";
	
		var nonce = oauth.nonce(32);
		var ts = Math.floor(new Date().getTime() / 1000);
		var timestamp = ts.toString();
	
		var accessor = {
			"consumerSecret": consumerSecret,
			"tokenSecret": tokenSecret
		};
		
		
		var params = {
			"status":postSummary,
			"oauth_version": "1.0",
			"oauth_consumer_key": oauth_consumer_key,
			"oauth_token": oauth_token,
			"oauth_timestamp": timestamp,
			"oauth_nonce": nonce,
			"oauth_signature_method": "HMAC-SHA1"
		};
		var message = {
			"method": "POST",
			"action": urlLink,
			"parameters": params
		};
		
	
		//lets create signature
		oauth.SignatureMethod.sign(message, accessor);
		var normPar = oauth.SignatureMethod.normalizeParameters(message.parameters);
		console.log("Normalized Parameters: " + normPar);
		var baseString = oauth.SignatureMethod.getBaseString(message);
		console.log("BaseString: " + baseString);
		var sig = oauth.getParameter(message.parameters, "oauth_signature") + "=";
		console.log("Non-Encode Signature: " + sig);
		var encodedSig = oauth.percentEncode(sig); //finally you got oauth signature
		console.log("Encoded Signature: " + encodedSig);
	
		Parse.Cloud.httpRequest({
			method: 'POST',
			url: urlLink,
			headers: {
				"Authorization": 'OAuth oauth_consumer_key="'+oauth_consumer_key+'", oauth_nonce=' + nonce + ', oauth_signature=' + encodedSig + ', oauth_signature_method="HMAC-SHA1", oauth_timestamp=' + timestamp + ',oauth_token="'+oauth_token+'", oauth_version="1.0"'
			},
			body: "status="+status,
			success: function(httpResponse) {
				response.success(httpResponse.text);
			},
			error: function(httpResponse) {
				response.error('Request failed with response ' + httpResponse.status + ' , ' + httpResponse);
			}
		});
	});
	
For getting timeline:

	Parse.Cloud.define("Timeline", function(request, response) {
		var urlLink = 'https://api.twitter.com/1.1/statuses/home_timeline.json';
	
		var consumerSecret = "<your_consumer_secret>";
		var tokenSecret = "<your_secret_token_here>";
		var oauth_consumer_key = "<your_oauth_consumer_key>";
		var oauth_token = "<your_oauth_token_here>";
	
		var nonce = oauth.nonce(32);
		var ts = Math.floor(new Date().getTime() / 1000);
		var timestamp = ts.toString();
	
		var accessor = {
			"consumerSecret": consumerSecret,
			"tokenSecret": tokenSecret
		};
		
		
		var params = {
			"oauth_version": "1.0",
			"oauth_consumer_key": oauth_consumer_key,
			"oauth_token": oauth_token,
			"oauth_timestamp": timestamp,
			"oauth_nonce": nonce,
			"oauth_signature_method": "HMAC-SHA1"
		};
		var message = {
			"method": "GET",
			"action": urlLink,
			"parameters": params
		};
		
	
		//lets create signature
		oauth.SignatureMethod.sign(message, accessor);
		var normPar = oauth.SignatureMethod.normalizeParameters(message.parameters);
		console.log("Normalized Parameters: " + normPar);
		var baseString = oauth.SignatureMethod.getBaseString(message);
		console.log("BaseString: " + baseString);
		var sig = oauth.getParameter(message.parameters, "oauth_signature") + "=";
		console.log("Non-Encode Signature: " + sig);
		var encodedSig = oauth.percentEncode(sig); //finally you got oauth signature
		console.log("Encoded Signature: " + encodedSig);
	
		Parse.Cloud.httpRequest({
			method: 'GET',
			url: urlLink,
			headers: {
				"Authorization": 'OAuth oauth_consumer_key="'+oauth_consumer_key+'", oauth_nonce=' + nonce + ', oauth_signature=' + encodedSig + ', oauth_signature_method="HMAC-SHA1", oauth_timestamp=' + timestamp + ',oauth_token="'+oauth_token+'", oauth_version="1.0"'
			},
			body: {
			},
			success: function(httpResponse) {
				response.success(httpResponse.text);
			},
			error: function(httpResponse) {
				response.error('Request failed with response ' + httpResponse.status + ' , ' + httpResponse);
			}
		});
	});
	
For deleting a particular tweet:

	Parse.Cloud.define("Delete", function(request, response) {
		var urlLink = 'https://api.twitter.com/1.1/statuses/destroy/'+request.params.id+'.json';
	
		var consumerSecret = "<your_consumer_secret>";
		var tokenSecret = "<your_secret_token_here>";
		var oauth_consumer_key = "<your_oauth_consumer_key>";
		var oauth_token = "<your_oauth_token_here>";
	
		var nonce = oauth.nonce(32);
		var ts = Math.floor(new Date().getTime() / 1000);
		var timestamp = ts.toString();
	
		var accessor = {
			"consumerSecret": consumerSecret,
			"tokenSecret": tokenSecret
		};
		
		
		var params = {
			"oauth_version": "1.0",
			"oauth_consumer_key": oauth_consumer_key,
			"oauth_token": oauth_token,
			"oauth_timestamp": timestamp,
			"oauth_nonce": nonce,
			"oauth_signature_method": "HMAC-SHA1"
		};
		var message = {
			"method": "POST",
			"action": urlLink,
			"parameters": params
		};
		
	
		//lets create signature
		oauth.SignatureMethod.sign(message, accessor);
		var normPar = oauth.SignatureMethod.normalizeParameters(message.parameters);
		console.log("Normalized Parameters: " + normPar);
		var baseString = oauth.SignatureMethod.getBaseString(message);
		console.log("BaseString: " + baseString);
		var sig = oauth.getParameter(message.parameters, "oauth_signature") + "=";
		console.log("Non-Encode Signature: " + sig);
		var encodedSig = oauth.percentEncode(sig); //finally you got oauth signature
		console.log("Encoded Signature: " + encodedSig);
	
		Parse.Cloud.httpRequest({
			method: 'POST',
			url: urlLink,
			headers: {
				"Authorization": 'OAuth oauth_consumer_key="'+oauth_consumer_key+'", oauth_nonce=' + nonce + ', oauth_signature=' + encodedSig + ', oauth_signature_method="HMAC-SHA1", oauth_timestamp=' + timestamp + ',oauth_token="'+oauth_token+'", oauth_version="1.0"'
			},
			body: {
			},
			success: function(httpResponse) {
				response.success(httpResponse.text);
			},
			error: function(httpResponse) {
				response.error('Request failed with response ' + httpResponse.status + ' , ' + httpResponse);
			}
		});
	});


Retreiving User Timeline:

	Parse.Cloud.define("UserTimeline", function(request, response) {
	        var screen_name = <screen_name>
	        var count = <count>
		var urlLink = 'https://api.twitter.com/1.1/statuses/user_timeline.json?screen_name='+screen_name+'&count="+count;
	
		var consumerSecret = "<your_consumer_secret>";
		var tokenSecret = "<your_secret_token_here>";
		var oauth_consumer_key = "<your_oauth_consumer_key>";
		var oauth_token = "<your_oauth_token_here>";
	
		var nonce = oauth.nonce(32);
		var ts = Math.floor(new Date().getTime() / 1000);
		var timestamp = ts.toString();
	
		var accessor = {
			"consumerSecret": consumerSecret,
			"tokenSecret": tokenSecret
		};
		
		
		var params = {
			"oauth_version": "1.0",
			"oauth_consumer_key": oauth_consumer_key,
			"oauth_token": oauth_token,
			"oauth_timestamp": timestamp,
			"oauth_nonce": nonce,
			"oauth_signature_method": "HMAC-SHA1"
		};
		var message = {
			"method": "POST",
			"action": urlLink,
			"parameters": params
		};
		
	
		//lets create signature
		oauth.SignatureMethod.sign(message, accessor);
		var normPar = oauth.SignatureMethod.normalizeParameters(message.parameters);
		console.log("Normalized Parameters: " + normPar);
		var baseString = oauth.SignatureMethod.getBaseString(message);
		console.log("BaseString: " + baseString);
		var sig = oauth.getParameter(message.parameters, "oauth_signature") + "=";
		console.log("Non-Encode Signature: " + sig);
		var encodedSig = oauth.percentEncode(sig); //finally you got oauth signature
		console.log("Encoded Signature: " + encodedSig);
	
		Parse.Cloud.httpRequest({
			method: 'POST',
			url: urlLink,
			headers: {
				"Authorization": 'OAuth oauth_consumer_key="'+oauth_consumer_key+'", oauth_nonce=' + nonce + ', oauth_signature=' + encodedSig + ', oauth_signature_method="HMAC-SHA1", oauth_timestamp=' + timestamp + ',oauth_token="'+oauth_token+'", oauth_version="1.0"'
			},
			body: {
			},
			success: function(httpResponse) {
				response.success(httpResponse.text);
			},
			error: function(httpResponse) {
				response.error('Request failed with response ' + httpResponse.status + ' , ' + httpResponse);
			}
		});
	});
